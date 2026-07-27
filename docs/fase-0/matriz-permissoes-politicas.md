# Matriz de permissões e políticas

**Entregável da Fase 0** (Documento 2, §34.5 e §34.6) · **Estado:** PREENCHIDA, com verificações
pendentes · **Data:** 2026-07-27 · **Autor:** agente

> **Fonte canônica da matriz é o Documento 2, §34.5.** Este documento **preenche** o entregável
> da Fase 0; onde divergir da fonte, a fonte prevalece e a divergência é bug (núcleo §0).

## Como ler

Cada permissão declara **onze campos**. O campo mais importante não é a política — é
**a alternativa considerada e por que não atende**: é ele que a declaração de escopo mínimo exige,
e é ele que falta quando uma declaração é rejeitada.

| Marca | Significado |
|---|---|
| ✅ | Estabelecido pelo corpus, com fonte oficial já citada no Documento 2, §43 |
| ⚠️ `VERIFICAR:` | Afirmação que **não** posso sustentar sem a fonte oficial. Pergunta exata registrada |
| 🚫 | Permissão **não pedida**, por decisão registrada |

**Limitação de fonte, a mesma do parecer de classificação:** a política de rede deste ambiente
bloqueia `support.google.com` e `developers.google.com`. O que está marcado ✅ vem do corpus com
referência oficial no Documento 2, §43; o que está marcado ⚠️ **não foi conferido por mim**.

---

## 1. Resumo

| Permissão / API | Pedida? | Política | Declaração no Console | Fase |
|---|---|---|---|---|
| `INTERNET`, `ACCESS_NETWORK_STATE` | sim | — | — | 1 |
| `POST_NOTIFICATIONS` | sim | — | — | 1 |
| `USE_BIOMETRIC` | sim | — | — | 1 |
| `RECEIVE_BOOT_COMPLETED` | sim | — | — | 1 |
| `ACCESS_COARSE_LOCATION` | sim | Permissões sensíveis | justificativa de funcionalidade central | 1 |
| `ACCESS_FINE_LOCATION` | sim | Permissões sensíveis **+ escopo mínimo** | justificativa **+ declaração de escopo mínimo** | 1 · gate na **7** |
| `ACCESS_BACKGROUND_LOCATION` | sim | Localização em segundo plano | **formulário + vídeo** | 1 · gate na **7** |
| `FOREGROUND_SERVICE` | **depende do ADR-0007** | Serviços em primeiro plano | tipo declarado | 1 |
| `FOREGROUND_SERVICE_LOCATION` | **depende do ADR-0007** | idem | descrição + **vídeo por tipo** | 1 |
| `FOREGROUND_SERVICE_DATA_SYNC` | **improvável** — ver §2.7 | idem | idem | — |
| `FOREGROUND_SERVICE_SPECIAL_USE` | **depende do ADR-0007** | idem | subtipo + **revisão da Play** | 1 |
| `SCHEDULE_EXACT_ALARM` | **depende do ADR-0007** | Alarmes exatos | declaração | 1 |
| `USE_EXACT_ALARM` | 🚫 **fora de alcance** | idem | — | — |
| `REQUEST_IGNORE_BATTERY_OPTIMIZATIONS` | ⚠️ a decidir | ⚠️ `VERIFICAR:` | ⚠️ `VERIFICAR:` | 1 |
| `USE_FULL_SCREEN_INTENT` | 🚫 **não é premissa** | Serviço em primeiro plano e intent de tela cheia | — | — |
| `com.android.vending.BILLING` | sim | Pagamentos | — | 5 |
| `READ_CONTACTS` | 🚫 **evitada, decidido** | Contatos | — | — |
| Accessibility Service | 🚫 **proibição absoluta** | Acessibilidade | — | — |
| `READ_SMS`, `RECEIVE_SMS` | 🚫 **não pedidas** | SMS e chamadas | — | — |
| `CAMERA`, `RECORD_AUDIO` | 🚫 fora do MVP | — | — | — |
| `QUERY_ALL_PACKAGES` | 🚫 não usada | Visibilidade de pacotes | — | — |
| Play Integrity | 🚫 fora do núcleo | — | — | — |

---

## 2. Detalhamento

### 2.1 `ACCESS_COARSE_LOCATION` e `ACCESS_FINE_LOCATION`

| Campo | Conteúdo |
|---|---|
| **Finalidade concreta** | Obter a última localização conhecida para exibi-la ao contato autorizado **durante protocolo de emergência**, com idade, precisão e fonte (Doc 2, §13.2 e §13.3) |
| **Quando é solicitada** | Em contexto, no fluxo de ativação do Modo Rua — **nunca no onboarding em bloco** (Doc 2, §41; núcleo §6) |
| **Política aplicável** | Permissões e APIs sensíveis — necessidade para a funcionalidade central. **`FINE` persistente:** política de **escopo mínimo e botão de localização** |
| **Declaração exigida** | Justificativa de funcionalidade central. Para `FINE` persistente, **declaração própria** no Play Console justificando por que **o botão de localização e `ACCESS_COARSE_LOCATION` não atendem** |
| **Alternativa considerada, e por que não atende** | **(a) Só `COARSE`:** precisão de centenas de metros a quilômetros. O produto entrega a última localização a quem vai procurar uma pessoa em situação de roubo; um raio de quilômetro não distingue "está na rua X" de "está em outro bairro", e o Documento 3 §26.3 obriga exibir precisão — o contato veria um número que inviabiliza a ação. **(b) Botão de localização do sistema:** exige interação do usuário no momento da coleta. O cenário do produto é **exatamente aquele em que o usuário não pode interagir** — aparelho tomado, tela bloqueada, usuário sob coação ou já sem o aparelho. Um mecanismo que requer toque não cobre o caso que justifica o produto |
| **Se negada** | Modo degradado funcional declarado: a sessão ativa, o check-in funciona, o vigilante escala — **com menos informação** (Doc 3, §19.1). Nunca bloqueio total do aplicativo |
| **Ramificação por API** | — |
| **Fase** | Implementação na **1**; a declaração de escopo mínimo é **gate da Fase 7** |
| **Risco de rejeição** | Médio. Mitigação: a justificativa acima é escrita antes, não improvisada na submissão |
| **Situação** | ✅ A política de escopo mínimo prevê fiscalização para o fim de outubro de 2026, **apenas para apps que alvejam Android 17+**. O produto alveja 36 e só precisa alvejar 37 por volta de ago/2027 — por isso o efeito prático é **gate da Fase 7**, não bloqueio agora (Doc 2, §34.5, com fonte oficial) |

⚠️ `VERIFICAR:` a declaração de escopo mínimo é submetida **na listagem** ou **por formulário
próprio**? Existe modelo publicado do que ela precisa conter?

### 2.2 `ACCESS_BACKGROUND_LOCATION`

| Campo | Conteúdo |
|---|---|
| **Finalidade concreta** | Coletar localização durante a sessão com o aplicativo em segundo plano — que é o estado normal do produto — e permitir criar serviço em primeiro plano do tipo `location` com o app em segundo plano |
| **Quando é solicitada** | **Depois** de `FINE`/`COARSE` concedidas, em tela própria com explicação prévia, no fluxo de ativação. Permissões progressivas (Doc 2, §13.3) |
| **Política aplicável** | Localização em segundo plano |
| **Declaração exigida** | **Formulário no Play Console + vídeo de demonstração** |
| **Distinção que a v2.0 do Doc 2 errava, e que importa aqui** | ✅ A política **admite um conjunto de recursos centrais**, todos documentados e promovidos de forma proeminente na descrição — **a cerca de proximidade não fica barrada**. O que é **único** é o **recurso principal informado no formulário**. Produto e descrição: conjunto. Formulário: um principal (Doc 2, §34.5, MOD-02) |
| **Qual é o recurso principal** | **`[ABERTO — FASE 0]`** — decisão do **ADR-0008**, após verificar o formulário real no Play Console. **Nenhum agente escreve a declaração nem alinha título, descrição ou capturas a uma escolha antes do ADR** (mód. 10, §7) |
| **Alternativa considerada, e por que não atende** | **(a) Coletar só em primeiro plano:** o produto existe para o momento em que o usuário perdeu o controle do aparelho. Coleta só com app aberto cobre o cenário em que ele não é necessário. **(b) Coletar só sob FGS iniciado pelo usuário:** o FGS do tipo `location` **não pode ser criado com o app em segundo plano sem esta permissão** ✅ — a alternativa depende da permissão que se queria evitar. **(c) Cerca de proximidade sem background:** geofencing depende de localização em segundo plano (Doc 2, §13.4) |
| **Se negada** | **Plano B obrigatório e já declarado:** o produto permanece útil sem localização em segundo plano, em **modo degradado real e declarado** (Doc 2, §34.6). A garantia externa não depende de localização — depende de o servidor saber da sessão |
| **Fase** | Implementação na **1**; declaração e vídeo são entregáveis da **0**; aprovação é gate da **7** |
| **Risco de rejeição** | **Alto — é hipótese crítica do Documento 1, §24.2.** Sem caminho de aprovação, é condição de interrupção (Doc 1, §32) |

⚠️ `VERIFICAR:` o formulário real pede **um** recurso principal ou aceita conjunto? Quais são as
opções literais? O vídeo é exigido por permissão, por tipo de FGS, ou ambos?

### 2.3 `POST_NOTIFICATIONS`

| Campo | Conteúdo |
|---|---|
| **Finalidade concreta** | **É a única forma de pedir a confirmação** (Doc 3, §19.3). Não é acessório de interface |
| **Quando é solicitada** | No onboarding, **em contexto**, declarada como pré-condição de sessão |
| **Política aplicável** | — (permissão de runtime, sem política de loja própria) |
| **Declaração exigida** | — |
| **Alternativa considerada** | Nenhuma viável. Sem notificação não há pedido de confirmação, e sem pedido não há check-in |
| **Se negada** | **A sessão não inicia** — recusa explicada, com atalho para as configurações. ✅ Isso é bloqueio de uma função específica, não do aplicativo (Doc 2, §12.4, item 1) |
| **Ramificação por API** | ✅ **Só existe em API 33+.** Em 30–32 a pré-condição é avaliada por `areNotificationsEnabled()` **mais a importância do canal `check_in`**. Código versionado explicitamente; a matriz exige dois aparelhos abaixo de API 33 (mód. 10, §9) |
| **Fase** | **1** |
| **Verificação contínua** | ✅ O estado do canal é verificado **a cada avaliação de prazo**, não só na ativação. Desativação durante a sessão gera evento imediato ao servidor e faz o **primeiro** escalonamento virar verificação dirigida ao titular (Doc 2, §12.4, itens 2 e 3) |

### 2.4 `USE_FULL_SCREEN_INTENT` — 🚫 não é premissa

✅ Desde **22/01/2025**, entre os aplicativos que alvejam Android 14+, a permissão vem habilitada
por padrão apenas para os que têm funcionalidade de **chamada ou despertador**; os demais precisam
obtê-la do usuário (Doc 2, §12.4, com fonte oficial — Play Console Help, ref. 22).

**Decisão registrada:** não é premissa de nenhum fluxo. A visibilidade vem da **notificação
persistente da sessão**, com tempo restante e ação de confirmação antecipada. Se um dia for
usada, **entra por ADR** com o custo de fricção declarado.

⚠️ **Correção pendente de mecanismo (issue #14, ARB4-014):** o efeito está certo, o mecanismo não.
Pela documentação de plataforma, a permissão é concedida por padrão a **todos** os apps em
Android 14, e é a **Play Store que a revoga na instalação** para quem não tem chamada ou
despertador. **Consequência de teste:** o ensaio da M6 precisa rodar em build **instalado pela
Play** (faixa interna), nunca por APK lateral — um APK lateral nunca passa pela revogação e
mostraria a permissão concedida, resultado oposto ao do usuário real.

### 2.5 `SCHEDULE_EXACT_ALARM` e `USE_EXACT_ALARM`

| Campo | Conteúdo |
|---|---|
| **Finalidade concreta** | Disparar o pedido de confirmação no prazo — **se** o ADR-0007 escolher alarme exato |
| **Política aplicável** | Alarmes exatos |
| **Declaração exigida** | Declaração de uso |
| **`USE_EXACT_ALARM`** | 🚫 ✅ **Fora de alcance.** A variante auto-concedida é restrita a **despertador, timer e calendário** (Doc 2, §34.5). O Modo Rua não é nenhum dos três, e reivindicá-la seria afirmação falsa de categoria |
| **Alternativa considerada** | É o próprio objeto do **ADR-0007**: FGS de sessão, alarme exato ou WorkManager com reavaliação. Ver §2.7 |
| **Se negada ou revogada** | ✅ **Não degrada — apaga o disparo.** A permissão é negada por padrão em instalações novas, **revogável pelo usuário e pelo sistema**, e a revogação **cancela os alarmes já agendados**. É por isso que a perda de capacidade de disparo local recebe tratamento próprio: evento imediato ao servidor, capacidade reduzida declarada na interface, e o **primeiro** escalonamento vira verificação dirigida ao titular (Doc 2, §12.4, item 6) |
| **Ramificação por API** | ✅ muda em **31, 33 e 34** |
| **Fase** | **1**, condicionada ao ADR-0007 |
| **Medição associada** | ⚠️ `VERIFICAR:` **comportamento observado de revogação pelo sistema** — marcador aberto no Doc 2 §12.4, item 6, atribuído à medição da Fase 0 |

### 2.6 `RECEIVE_BOOT_COMPLETED`

| Campo | Conteúdo |
|---|---|
| **Finalidade concreta** | Reidratar a máquina de estados e reagendar workers após reinício (mód. 10, §6) |
| **Política aplicável** | — |
| **Fato de plataforma que decide o desenho** | ✅ Apps que alvejam Android 15+ **não podem** iniciar, a partir de receptor de `BOOT_COMPLETED`, FGS dos tipos `dataSync`, `camera`, `mediaPlayback`, `phoneCall`, `mediaProjection` e `microphone`. **`location` NÃO está na lista** — é o fato que decide se o `BootReceiver` pode retomar a sessão **com serviço** (Doc 2, §35, com fonte oficial) |
| **Se indisponível** | ✅ Retomada impossível gera **aviso honesto ao usuário e evento ao servidor**, que passa a registrar perda de cobertura. **Nunca silêncio** (Doc 2, §35) |
| **Fase** | **1** · **Medição M2** |

### 2.7 Serviço em primeiro plano — o bloco que depende do ADR-0007

**`[ABERTO — FASE 0]`.** O mecanismo de disparo é decidido pelo **ADR-0007**, após a medição.
Nenhum agente escolhe entre FGS, alarme exato e WorkManager antes disso (mód. 10, §5).

| Tipo | Situação | Fato que a decide |
|---|---|---|
| **`location`** | candidato vivo | ✅ Sem timeout de 6 h/24 h · ✅ **pode** subir de `BOOT_COMPLETED` · exige `ACCESS_BACKGROUND_LOCATION` para ser criado em segundo plano |
| **`dataSync`** | ✅ **inviável para sessão longa** | Limitado a **6 h por 24 h**, com `Service.onTimeout()` e **exceção fatal** se o serviço não parar · **não pode** subir de `BOOT_COMPLETED` |
| **`specialUse`** | candidato, com risco próprio | Exige **subtipo declarado** e está **sujeito a revisão da Play** — dependência de aprovação, não só de medição |

**Cota de jobs — o fato que a medição M9 existe para quantificar.** ✅ Em Android **16 ou
superior**, **para todo aplicativo, independente do `targetSdk`**, jobs iniciados a partir de um
serviço em primeiro plano passam a obedecer às cotas de execução, **incluindo os criados por
WorkManager**. Consequência prática: sync, purga e retomada disparados a partir do FGS de sessão
estão **sob cota** (Doc 2, §35, com fonte oficial).

> **O parecer de classificação já removeu uma restrição que se supunha existir aqui.**
> `WorkManager` puro **não** está eliminado por política de monitoramento — ver
> `parecer-classificacao-monitoramento.md`, §5.1. A notificação persistente que o produto terá
> vem do FGS (se escolhido), do princípio antivigilância e da affordance de sessão — **não** da
> política de aplicativos de monitoramento.

**Declaração exigida:** descrição e **vídeo por tipo**, com o tipo compatível com o timeout
aplicável e com a retomada pós-boot (mód. 50, §8).

### 2.8 `REQUEST_IGNORE_BATTERY_OPTIMIZATIONS` — ⚠️ a decidir, com verificação pendente

| Campo | Conteúdo |
|---|---|
| **Finalidade concreta** | Reduzir o atraso de disparo sob otimização agressiva de fabricante |
| **Tensão de produto que já existe** | ✅ **O usuário nunca é instruído a desativar toda a proteção de bateria** (Doc 2, §35). Pedir isenção não é o mesmo que instruir a desativar tudo — mas é vizinho, e a fronteira precisa ser explícita |
| **Se negada** | Tratada como **perda de capacidade de disparo local**: evento imediato ao servidor, capacidade reduzida declarada, primeiro escalonamento dirigido ao titular (Doc 2, §12.4, item 6) |
| **Situação** | ⚠️ **Não decidida.** Depende do ADR-0007 e da medição M1 — se o atraso medido **sem** isenção já for compatível com a promessa, a permissão não é pedida, e essa é a saída preferível |

⚠️ `VERIFICAR:` a política da Play restringe `REQUEST_IGNORE_BATTERY_OPTIMIZATIONS` a casos de uso
qualificados? Há lista fechada? Existe declaração exigida no Console? **Pergunta exata:** um
aplicativo de segurança pessoal com temporizador de check-in está entre os casos aceitáveis?

### 2.9 Permissões de rotina

`INTERNET` e `ACCESS_NETWORK_STATE` — sincronização, heartbeat, registro de sessão. Sem política
própria. Sem declaração.

`USE_BIOMETRIC` — confirmação forte via `BiometricPrompt`, com `DEVICE_CREDENTIAL` permitido
(Doc 2, §14.3). ✅ **Biometria é conveniência, não requisito**: sua indisponibilidade após
reinício é comportamento normal, e o fallback é o **credencial de tela de bloqueio do aparelho**
(Doc 3, §28.1 e §28.2). Fase 1.

`com.android.vending.BILLING` — Play Billing para assinaturas. Fase 5.

### 2.10 O que o produto 🚫 **não** pede, e por quê

| Item | Motivo |
|---|---|
| **`READ_CONTACTS`** | ✅ **Decidido:** evitada por entrada manual ou seletor do sistema (Doc 2, §34.5). Evita a política de contatos inteira. ⚠️ **Issue #16 / ARB4-015:** falta linha no checklist de publicação confirmando que nenhuma permissão de contatos é pedida — o claim regride em silêncio no dia em que alguém acrescentar "importar dos contatos" |
| **Accessibility Service** | **Proibição absoluta** — núcleo §6, Doc 2 §41, Doc 1 §27.13. Usar acessibilidade para controlar ou observar outros apps é violação, e o produto não depende dela como atalho |
| **`READ_SMS`, `RECEIVE_SMS`** | O aplicativo **não lê SMS**. O SMS é canal de alerta **do servidor para o contato**, não do aparelho. ✅ Isso torna irrelevante o `VERIFICAR:` do Doc 2 §22.2 sobre atraso de mensagem com código no Android 17 — **enquanto** nenhum fluxo de segundo fator do contato usar código por SMS. Se passar a usar, o item precisa ser verificado **antes**, e a alternativa é TOTP ou passkey |
| **`CAMERA`, `RECORD_AUDIO`** | Gravação e câmera ocultas estão fora do MVP (Doc 1, §21.3) e são anti-padrão (Doc 3, §52) |
| **`QUERY_ALL_PACKAGES`** | Nenhuma funcionalidade a exige. Ocultar ou bloquear apps de terceiros está fora do escopo (Doc 1, §11) |
| **Play Integrity** | ✅ Fora do núcleo do MVP. Se adotado, **apenas como sinal não bloqueante** — nunca bloqueia automaticamente uma vítima com aparelho modificado (Doc 2, §37.1) |

---

## 3. Declarações de loja que não são permissões

| Declaração | Conteúdo | Situação |
|---|---|---|
| **Classificação como aplicativo de monitoramento** | Parecer escrito | ✅ **`parecer-classificacao-monitoramento.md`** — provisório, 6 verificações |
| **`isMonitoringTool`** | Posição de Fase 0: **não declarar**, salvo se a revisão formal determinar enquadramento obrigatório. **Não inserir no `spike/` nem no manifesto-base** | ⏳ ADR **não** proposto agora, por instrução do fundador |
| **Data Safety** | Coerente com a matriz de tratamento do Documento 3, §31 | ⏳ rascunho pendente |
| **Exclusão de conta** | ✅ Fluxo no aplicativo **e página web pública**, mais endpoint | Fase 2 |
| **Classificação de conteúdo e público-alvo** | ✅ **18+**, coerente com a restrição do Doc 3, §46. Declaração de idade no cadastro | Fase 5 |
| **Anúncios** | Não há. Localização **nunca** vai para publicidade (Doc 3, §45) | — |
| **Vídeo de demonstração** | Por permissão de segundo plano e por tipo de FGS | ⏳ roteiro pendente |
| **Divulgação proeminente** | Texto na descrição promovendo o **conjunto** de recursos centrais que justificam a localização em segundo plano | ⏳ pendente, depende do ADR-0008 |

---

## 4. O que trava, e em quem

**Nada aqui trava a coleta em aparelho.** As pendências são de mesa e de Play Console.

| Pendência | Depende de | Destrava |
|---|---|---|
| 4 `VERIFICAR:` desta matriz (§2.1, §2.2, §2.8) | **conta no Play Console** | declaração de escopo mínimo, ADR-0008, decisão sobre isenção de bateria |
| Recurso principal de segundo plano | verificação do formulário real | **ADR-0008** |
| Tipo de FGS e alarme exato | **medição M1, M2, M3, M5, M9** | **ADR-0007** |
| Isenção de bateria | medição M1 **sem** isenção | se o atraso já for compatível, a permissão não é pedida |

> **A conta do Play Console é o gargalo dos entregáveis de mesa.** Quatro `VERIFICAR:` desta
> matriz e seis do parecer dependem dela, e nenhum exige aparelho.

## 5. Revisão obrigatória

✅ Esta matriz é **revisada a cada release que toque permissões ou comportamento de fundo**
(mód. 50, §8) e **a cada release**, contra mudança de política da loja (Doc 2, §42). Matriz
desatualizada é risco declarado: *"Mudança de política da loja | Alto | Matriz revisada a cada
release, plano B declarado"*.
