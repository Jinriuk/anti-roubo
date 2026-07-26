# Documento 4 — Módulo 10: Android

**Versão:** 2.2 | **Substitui:** módulo 10 v1.0, v2.0 e v2.1 (correções ARB2 e ARB3)
Aplica-se a todo código em `android/`. Pressupõe `00-nucleo.md` no contexto. Arquitetura de referência: Documento 2, §8 a §17 e §35.

---

## 1. Estrutura e regras de dependência

Estrutura de módulos conforme Documento 2, §8. Regras de dependência (violação falha o build, não apenas reprova a PR — núcleo §11):

| Regra | Motivo |
|---|---|
| `feature:*` depende apenas de `domain:*` e `core:*` | Features não conhecem infraestrutura |
| `domain:*` depende apenas de Kotlin puro, `core:common` e `core:model` | Regras de negócio testáveis sem Android |
| `domain:*` NÃO importa `android.*`, Room, Retrofit, Firebase ou qualquer SDK | Idem |
| UI nunca acessa `core:network` ou `core:database` diretamente | Todo dado passa por repositório e caso de uso |
| Entidades Room e DTOs de rede nunca chegam à UI; sempre mapear para modelo de domínio | Mudança de schema não pode quebrar telas |
| SDK externo (FCM, Billing, mapas) é encapsulado em interface própria dentro de `core:*` | Substituível por fake em teste; trocável por ADR |
| Dependência circular entre módulos é proibida | Verificado no Gradle |
| `app` apenas compõe DI e navegação | Nenhuma regra de negócio no módulo app |

Ao criar arquivo novo, o agente declara na PR em qual módulo e camada ele entra e por quê. Se não há lugar óbvio, a estrutura está errada ou a tarefa está: parar e perguntar.

---

## 2. Kotlin

- `val` por padrão; `var` exige motivo local evidente.
- Modelos de domínio: `data class` imutáveis; coleções imutáveis expostas (`List`, não `MutableList`).
- Nulabilidade: proibido `!!` em produção sem comentário justificando; preferir `requireNotNull(x) { "motivo" }`, que documenta a invariante.
- Erros de domínio: `sealed interface` ou `Result` por fluxo, não exceção. Exceção é para bug e condição irrecuperável, não para controle de fluxo.

```kotlin
// CORRETO: erro é parte do contrato
sealed interface ActivateStreetModeError {
    data object MissingLocationPermission : ActivateStreetModeError
    data object MissingNotificationChannel : ActivateStreetModeError
    data object SessionAlreadyActive : ActivateStreetModeError
    data class Persistence(val cause: Throwable) : ActivateStreetModeError
}
suspend fun activate(config: StreetModeConfig): Result<StreetModeSession, ActivateStreetModeError>

// INCORRETO: quem chama não sabe o que pode falhar
suspend fun activate(config: StreetModeConfig): StreetModeSession
```

- Coroutines: structured concurrency sempre. Proibido `GlobalScope`. Escopos: `viewModelScope` na apresentação, escopo injetado com `SupervisorJob` em componentes de longa vida. Dispatchers **injetados** (interface `DispatcherProvider`), nunca hardcoded.
- `runBlocking` apenas em `main` de ferramenta e em testes.
- `catch` genérico que engole erro é proibido em fluxo crítico: capturar tipo específico, registrar sanitizado e propagar como erro de domínio.
- Detekt e ktlint com zero novos warnings; supressão exige comentário e issue.

---

## 3. Jetpack Compose

- Fluxo unidirecional: Composable emite eventos → ViewModel → caso de uso; estado volta por `StateFlow` coletado com `collectAsStateWithLifecycle()`.
- Composables de tela são **stateless**: recebem `UiState` e lambdas. `remember` só para detalhe visual.
- Proibido em Composable: regra de negócio, chamada a repositório, acesso a `Context` para lógica.
- Efeitos colaterais apenas em `LaunchedEffect` ou `DisposableEffect` com chave correta; nunca no corpo da composição.
- Um `UiState` por tela, incluindo carregamento, erro, **modo degradado** (permissão negada, offline) e **estado de cobertura** (Documento 2, §11.1). Toda tela crítica desenha seu estado degradado; "não tratei porque é raro" é reprovação.
- **O estado de cobertura é obrigatório na tela de sessão ativa**, com o texto do Documento 2, §11.1 e as transições da tabela do **§11.2**. Cada estado tem texto próprio: usar o texto de `COBERTURA_LOCAL` em `COBERTURA_SUSPENSA` é bug, porque nesse estado o vigilante está armado e o alerta vai ocorrer. Não exibir cobertura, ou exibir texto que não corresponde ao estado, é bloqueador de release (Documento 3, §51).
- Parâmetros estáveis; listas grandes com `key` em `LazyColumn`.
- `@Preview` para todo componente do design system e para os estados principais das telas críticas: normal, degradado, cobertura reduzida e protocolo aberto.
- Acessibilidade mínima obrigatória: `contentDescription` em ícones acionáveis, alvos de toque ≥ 48dp, textos escaláveis, nenhuma informação transmitida só por cor. Em emergência o usuário está sob estresse; acessibilidade aqui é requisito funcional.

---

## 4. Máquina de estados da sessão

Referência canônica: **Documento 2, §10.1**. Este módulo não redefine estados.

- Estados e transições implementados em `domain:streetmode`, em Kotlin puro, como função de transição explícita: `(estado, evento) -> novo estado + efeitos`. Proibido espalhar `if` de estado por ViewModels.
- Toda transição gera evento append-only persistido no Room **na mesma transação** que atualiza o estado.
- Evento persistido nunca é alterado ou deletado por lógica de negócio; correção é novo evento.
- O estado atual é persistido e **reidratável**: após kill do processo ou reboot, o app reconstrói o estado a partir do banco e recalcula prazos. Proibido estado crítico apenas em ViewModel ou memória.
- Transição inválida não é ignorada silenciosamente: registra `invalid_transition_detected`.
- **O aparelho emite `check_in_missed` e não escala.** Escalonamento é do servidor (Documento 2, §18.7). Nenhum código local abre protocolo, notifica contato ou decide emergência.
- Cobertura: teste para **todas** as transições válidas e para as inválidas relevantes (módulo 40).

---

## 5. Tempo e temporização

Referência: Documento 2, §12. Esta é a área de maior risco do produto.

- Interface `Clock` própria injetada em todo código que lê tempo. Proibido `System.currentTimeMillis()` ou `LocalDateTime.now()` fora da implementação real do `Clock`.
<!-- gerado de Documento 2, §12.3 — validado em CI; não editar aqui -->
- **Todo prazo persiste três valores:** `deadline_wall_utc`, `deadline_elapsed_ms` e `boot_id`.
  - `boot_id` igual ao atual → avalia por relógio monotônico (`elapsedRealtime`).
  - `boot_id` diferente → avalia por tempo de parede, com detecção de salto comparando o delta de parede ao delta monotônico desde o último ponto de sincronismo.
  - Salto detectado emite `clock_jump_detected` com a diferença medida.
- Timer em memória é apenas otimização de interface; a verdade está no banco.
- Ao abrir o app, ao receber boot e ao rodar worker: recalcular estado, tratando prazo já expirado.
- **Temporização local é legítima. O proibido é ação externa ou irreversível decidida apenas pelo relógio do aparelho** — a autoridade sobre prazo vencido para efeito externo é do servidor.
- Proibido `Thread.sleep` e `delay` como mecanismo de espera de negócio em produção. Backoff de retry é do WorkManager.
- O mecanismo de disparo do prazo está **`[ABERTO — FASE 0]`** (Documento 2, §12.2). Nenhum agente escolhe entre foreground service, alarme exato e WorkManager sem ADR-0007.

```kotlin
// CORRETO
class CheckInEvaluator(private val clock: Clock) {
    fun evaluate(checkIn: CheckIn, currentBootId: BootId): CheckInState =
        if (clock.isPastDeadline(checkIn, currentBootId)) CheckInState.MISSED else checkIn.state
}

// INCORRETO: intestável, frágil a ajuste de relógio e cego a reboot
fun evaluate(checkIn: CheckIn) =
    if (System.currentTimeMillis() > checkIn.graceDeadlineMillis) MISSED else checkIn.state
```

---

## 6. Trabalho em segundo plano

- WorkManager para todo trabalho persistente e diferível: sync, retry, token FCM, purga local, recuperação pós-boot. Constraints e backoff explícitos; nunca assumir execução pontual ao minuto.
- Foreground service somente em fluxo iniciado explicitamente pelo usuário, com tipo declarado correto e notificação visível honesta. Consequência do princípio anti-vigilância (Documento 3, §23): serviço visível não é limitação, é requisito.
- `BootReceiver` reidrata a máquina de estados e reagenda workers.
- **Restrições de plataforma que a implementação precisa respeitar** (Documento 2, §35 — linha de base Android 15, 16 e **17**):
  - serviço em primeiro plano do tipo `location` não pode ser criado com o app em segundo plano sem `ACCESS_BACKGROUND_LOCATION`;
  - apps que alvejam Android 15+ não podem iniciar, a partir de um receptor de `BOOT_COMPLETED`, serviços dos tipos `dataSync`, `camera`, `mediaPlayback`, `phoneCall`, `mediaProjection` e `microphone`. **`location` não está na lista** — é o que decide se o `BootReceiver` pode retomar a sessão com serviço;
  - apps que alvejam Android 15+ têm `dataSync` e `mediaProcessing` limitados a **6 h por 24 h**, com `Service.onTimeout()` e exceção fatal se não pararem. Serviço de sessão longa **não** usa `dataSync`;
  - em Android 16+, **para todo app, independente do `targetSdk`**, jobs iniciados a partir de um serviço em primeiro plano obedecem às cotas de execução, **incluindo os de WorkManager**. Consequência prática: sync, purga e retomada disparados a partir do FGS de sessão estão sob cota, e a medição da Fase 0 inclui a latência de worker com e sem o FGS ativo;
  - force-stop, pelo usuário ou por gerenciador de tarefas do fabricante, cancela alarmes e trabalho agendado;
  - `SCHEDULE_EXACT_ALARM` é negada por padrão em instalação nova, revogável pelo usuário **e pelo sistema**, e a revogação **cancela os alarmes já agendados** — tratar conforme §9.
  Toda hipótese de retomada é verificada em aparelho e documentada em `docs/fabricantes/<marca>.md` com aparelho e versão. **Retomada impossível gera aviso honesto ao usuário e evento ao servidor**, que passa a registrar perda de cobertura. Nunca silêncio.
- FCM é canal complementar de sincronização e alerta, nunca o único gatilho de lógica crítica (Documento 2, §26).
- Nenhum wakelock manual sem ADR.
- Adaptações por fabricante ficam isoladas em `core:*` com documentação do comportamento observado; nunca espalhadas por features.

---

## 7. Localização

Referência: Documento 2, §13; Documento 3, §26. Regras inegociáveis:

- Coleta por níveis (`economico`, `elevado`, `emergencial`) com duração e expiração; o nível emergencial expira automaticamente.
- Armazenar somente: latitude, longitude, precisão, timestamp, origem, nível e classe de retenção. Nada além disso.
- **Chave: `K_dados`, não `K_leitura`** (Documento 2, §14.3). A sincronização roda em worker, sem usuário presente, e chave que exige autenticação não pode ser usada por worker — sob `K_leitura`, a localização de emergência nunca sairia do aparelho roubado. A amostra é **apagada no ACK**; não há reencriptação e não existe amostra de localização sob `K_leitura`. O histórico legível vem do servidor.
- Proibido: rota contínua indefinida por padrão; localização em log, analytics, crash report, URL, push ou SMS; apresentar localização antiga como atual — sempre exibir idade, precisão e fonte.
- Permissões solicitadas progressivamente, no contexto de uso, com explicação prévia. Negação leva a modo degradado funcional, nunca a bloqueio total do app.
- Localização em segundo plano só como funcionalidade central, com a justificativa de loja preparada (Documento 2, §34.5). O produto pode promover um **conjunto** de recursos centrais; o **formulário** declara o recurso principal, e qual é ele está **`[ABERTO — FASE 0]`** (ADR-0008). Nenhum agente escreve a declaração nem alinha título, descrição ou capturas a uma escolha antes do ADR.
- Código de localização trata indisponibilidade como caso normal: o GPS pode não responder, e a última amostra conhecida com idade é resposta válida.
- Cercas de proximidade têm latência da ordem de minutos e dependem de localização em segundo plano. Nenhuma lógica assume transição imediata.

---

## 8. Armazenamento local e chaves

**Room:** sessões, check-ins, eventos, fila de sync, contatos, estados, localizações necessárias, recibos e **contador de sequência por instalação**. Escrita de evento e de estado na mesma transação. Migrations versionadas e testadas com `MigrationTestHelper`. **`fallbackToDestructiveMigration` é proibido em produção.**

**Proto DataStore:** preferências tipadas, flags e configuração de notificações. **Proibido** para listas de eventos, dados relacionais, identidade de instalação e contador de sequência, porque participa de backup.

**Identidade de instalação:** UUID gerado no primeiro início, gravado em diretório excluído do backup, nunca reutilizado, nunca restaurado (Documento 2, §4.7).

**Hierarquia de chaves** (Documento 2, §14.3), obrigatória:

| Chave | Configuração | Protege |
|---|---|---|
| `K_dados` | Keystore, **sem** `setUserAuthenticationRequired` | Fila de saída pendente de ACK, **inclusive coordenadas ainda não sincronizadas**, estado operacional, prazos, contador de `sequence` |
| `K_leitura` | Keystore, `setUserAuthenticationRequired(true)` com `setUserAuthenticationParameters(0, BIOMETRIC_STRONG \| DEVICE_CREDENTIAL)` — **autenticação a cada uso, timeout zero** | Histórico legível, cofre, áreas seguras |
| `K_confirmacao` | Keystore, EC P-256, `setUserAuthenticationRequired(true)`, autenticação a cada uso | Assina confirmação de check-in e encerramentos autenticados (Documento 2, §16.8); chave pública registrada no vínculo da instalação |

- **A confirmação é assinada offline.** A tupla é material do aparelho: `(installation_id, session_id, check_in_id, sequence, expected_next_checkin_at, boot_id, desafio_de_sessao?)`. **Não pedir nonce ao servidor para confirmar** — confirmar não depende de rede (núcleo §2.2), e o desafio de sessão, quando existe, veio da última resposta do servidor e está em cache. Ausência de desafio é caso normal na Fase 1 e em `COBERTURA_LOCAL`.
- **Uma autenticação produz uma assinatura.** Proibido cachear assinatura, assinar em lote ou configurar janela de validade nesta chave: é o único limite contra pré-assinatura por quem obtém um momento autenticado.
- Em aparelho **sem tela de bloqueio segura** a chave não é criável (`KeyguardManager#isDeviceSecure()`). O comportamento é a decisão **[PENDENTE — DECISÃO DO FUNDADOR]** do Documento 2, §14.3 e a pré-condição de entrada da Fase 1; o agente não escolhe.

- Desbloqueio por `BiometricPrompt` com `DEVICE_CREDENTIAL` permitido. **Timeout zero é obrigatório:** janela de validade deixaria o histórico legível para quem está com o aparelho recém-desbloqueado, que é o cenário de referência do Documento 3, §13. Custo de experiência declarado e aceito: cada abertura do histórico pede autenticação.
- `VERIFICAR:` semântica de invalidação por novo cadastro biométrico em chave que aceita `DEVICE_CREDENTIAL`; medir na Fase 0 antes de assumir qualquer comportamento.

- Todo registro carrega `key_version`; rotação por job de releitura e regravação.
- Invalidação detectada emite `local_key_invalidated`, envia diagnóstico e solicita revinculação. **Nunca falha em silêncio.**
- StrongBox quando disponível; ausência é limitação declarada, não bloqueio.
- **PIN interno não é chave e não desbloqueia chave.** Um segredo do aplicativo não satisfaz a exigência de autenticação do Keystore — só biometria ou o credencial de tela de bloqueio do aparelho satisfazem. O fallback quando a biometria está indisponível após reinício é o **credencial do aparelho**, cuja limitação de tentativas é imposta pelo hardware de verdade. O papel do PIN interno e a exigência de tela de bloqueio segura (`KeyguardManager#isDeviceSecure()`) estão **[PENDENTE — DECISÃO DO FUNDADOR]** (Documento 2, §14.3): nenhum agente implementa nenhuma das opções.
- Nada sensível em `SharedPreferences` plano, external storage ou backup automático. Por conta do `minSdk` 30, a exclusão exige os **dois** mecanismos: `android:fullBackupContent` (API ≤ 30) e `android:dataExtractionRules` (API ≥ 31) com `<cloud-backup>` **e** `<device-transfer>` — sem `<device-transfer>`, a migração aparelho-a-aparelho copia tudo, que é justamente o caminho testado na Fase 1. Lista de exclusões idêntica nos dois arquivos; ausência de um deles falha o build (núcleo §11). Decidido por ADR-0004.

**Retenção local** (Documento 2, §14.4): amostras de rotina 24 h ou até ACK; amostras de emergência até o protocolo resolvido + 24 h; eventos até ACK + 7 dias; histórico legível vem do servidor. Job de purga em WorkManager, classificado como funcionalidade crítica.

---

## 9. Notificações

- Canais fixos: `street_mode_status`, `check_in`, `emergency`, `trusted_contacts`, `account_security`, `billing`. Canal novo exige ADR.
- **Permissão de notificação e canal `check_in` habilitado são pré-condição de sessão** (Documento 2, §12.4). Sem eles, a sessão não inicia: recusa explicada, com atalho para as configurações. Isso não é bloquear o app.
- O estado do canal é verificado **a cada avaliação de prazo**, não apenas na ativação. Desativação durante a sessão gera evento imediato ao servidor e altera o primeiro escalonamento, que passa a ser verificação dirigida ao titular em vez de acionamento de contato.
- Payload de push mínimo. Proibido coordenada, endereço, nome completo, motivo detalhado, token ou link permanente (Documento 3, §43).
- Conteúdo em tela bloqueada genérico ("Existe uma atualização de segurança. Abra o aplicativo."), configurável pelo usuário.
- Notificação em tela cheia **não é premissa**: a permissão correspondente é concedida automaticamente apenas a apps cuja função central é despertador ou chamadas. A visibilidade vem da notificação persistente da sessão, com tempo restante e ação de confirmação antecipada.
- **Ação de notificação nunca conclui transição que exija confirmação forte.** A ação de confirmação antecipada **abre** a tela de confirmação; não grava `check_in_confirmed`. Toque em notificação é toque simples, e toque simples não conclui check-in (Documento 3, §13.3). A ação de acionamento manual de emergência na mesma notificação **pode** ser um toque único: escala na direção segura e reversível. A assimetria é deliberada.
- **Perda de capacidade de disparo local** — permissão de alarme exato revogada pelo usuário ou pelo sistema, isenção de bateria removida, tipo de serviço indisponível, canal desativado — gera evento imediato ao servidor, reduz a capacidade declarada na interface e faz o primeiro escalonamento virar verificação ao titular (Documento 2, §12.4, item 6). Verificar a cada avaliação de prazo.
- **Abaixo de API 33 não existe `POST_NOTIFICATIONS`:** a pré-condição de sessão é avaliada por `areNotificationsEnabled()` mais a importância do canal `check_in`. Código versionado explicitamente; a matriz exige dois aparelhos abaixo de API 33.
- Nenhuma ação crítica executada apenas por push: push aponta, backend confirma.
- Deep links validados; `PendingIntent` imutável; intents explícitos.
- Textos envolvendo contatos ou revogação seguem as regras de linguagem do Documento 3, §23.4. Quem escreve copy de notificação lê aquela seção antes.

---

## 10. Bateria e performance

- Metas: consumo de sessão compatível com uso diário; startup frio sem regressão; zero ANR novos. Os limiares numéricos estão **`[ABERTO — FASE 0]`** e entram por ADR-0012.
- Coleta de sinais em lote e por nível; sensores desligados fora de sessão.
- Macrobenchmark e Baseline Profiles no pipeline; regressão relevante bloqueia merge (limiares no módulo 40).
- Proibido polling agressivo "para garantir": a garantia vem de estado persistido, reavaliação em eventos do sistema e vigilante no servidor.
- Todo PR que toca coleta de sinais, workers, localização ou agendamento declara impacto esperado de bateria e como foi verificado.

---

## 11. Compatibilidade

- `minSdk` **30 provisório**, `targetSdk` **36**, conforme ADR-0002 (Documento 2, §35). O agente não altera esses valores por conta própria.
- Linha de base de comportamento: Android 15, 16 e **17** (estável desde 16/06/2026). "Android atual" na matriz significa 17.
- A faixa 30→36 ramifica em `POST_NOTIFICATIONS` (33), atributo de backup (31), tipos de serviço em primeiro plano (31, 34, 35, 36) e alarmes exatos (31, 33, 34). Todo código que dependa desses pontos declara o ramo e é testado em aparelho de cada lado da fronteira.
- Comportamento divergente de fabricante é documentado em `docs/fabricantes/<marca>.md`, com aparelho e versão testados.
- Nada é considerado funcional em segundo plano sem teste na matriz mínima de aparelhos (módulo 40, §5).

---

## 12. Checklist Android para PR

- [ ] Dependências entre módulos respeitam a tabela da §1
- [ ] Nenhum SDK externo fora de encapsulamento
- [ ] Estado crítico persistido e reidratável; nenhum timer só em memória
- [ ] Três bases de tempo persistidas; `Clock` e `DispatcherProvider` injetados
- [ ] Nenhuma decisão de efeito externo tomada localmente por relógio
- [ ] Estado de cobertura exibido nas telas de sessão
- [ ] Telas com estado degradado tratado
- [ ] Permissão de notificação e canal verificados na ativação e a cada prazo
- [ ] Chaves na hierarquia correta, com autenticação a cada uso; `key_version` gravado; invalidação tratada
- [ ] Nenhum segredo do aplicativo usado para desbloquear chave do Keystore
- [ ] Amostra de localização pendente sob `K_dados`, legível por worker, apagada no ACK
- [ ] Confirmação forte assinada por `K_confirmacao`; ação de notificação não conclui check-in
- [ ] Manifesto com os dois mecanismos de exclusão de backup, incluindo `<device-transfer>`
- [ ] Perda de capacidade de disparo local tratada e reportada ao servidor
- [ ] Purga local coberta por teste
- [ ] Nenhum dado sensível em log ou notificação (módulo 30, §2)
- [ ] Migrations Room testadas, se houver
- [ ] Strings de UI em recursos, pt-BR, revisadas contra Documento 3, §23.4 quando envolvem contatos ou alertas
- [ ] Detekt e ktlint sem novos warnings
