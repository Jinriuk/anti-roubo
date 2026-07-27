# Documento 4 — Regras de Engenharia para os Agentes
## Núcleo — versão 2.1

**Projeto:** Modo Rua — plataforma móvel de proteção pós-roubo
**Versão:** 2.3 | **Status:** Vigente | **Substitui:** núcleo 1.0, 2.0, 2.1 e 2.2
**Alteração da 2.2:** correções da segunda rodada de revisão (ARB2). Novidades que mudam o comportamento do agente: o Documento 4 deixa de ser fonte de conteúdo arquitetural (§0); os quatro itens `[ABERTO — FASE 0]` têm localização corrigida (§0); há itens **[PENDENTE — DECISÃO DO FUNDADOR]**, que o agente também não decide (§0); e a lista de gates cresceu (§11).
**Público:** qualquer agente de IA e qualquer humano que produza código, migration, configuração ou documentação neste projeto.

Este arquivo é o único que deve estar **sempre** no contexto. Os módulos entram por tarefa (§1).

---

## 0. Hierarquia de autoridade

Em qualquer conflito, vence a ordem:

1. Lei (LGPD) e políticas da Google Play;
2. **Documento 3** — Segurança, Privacidade e Ameaças;
3. **Documento 4** — este conjunto;
4. **Documento 5** — Roadmap, Critérios de Aceite e Matriz de Evidências;
5. **Documento 2** — Arquitetura Técnica;
6. **Documento 1** — Visão do Produto;
7. Instruções da tarefa atual.

**O Documento 4 não é fonte de conteúdo arquitetural, de estados, de contratos, de modelo de dados, de ameaças, nem de escopo, fase, critério de aceite ou evidência.** Onde um módulo reenunciar conteúdo dos Documentos 2, 3 **ou 5**, prevalece a fonte, e a reenunciação divergente é bug a corrigir — não conflito a resolver por hierarquia. Reenunciação só existe quando é útil ao agente, e nesse caso é **gerada e validada em CI** (§11), marcada no texto com a origem.

Se a tarefa contrariar um nível superior, o agente **para, reporta o conflito e aguarda decisão**. Não implementa "do jeito pedido" nem corrige silenciosamente "do jeito certo". Exceção só por ADR aprovado pelo fundador (§10).

**Itens marcados `[ABERTO — FASE 0]` não são lacuna de documentação: são decisões deliberadamente não tomadas.** Fechá-los sem ADR é violação grave. São quatro, e o marcador existe literalmente em cada um dos locais abaixo — se não estiver lá, é bug de documentação a reportar, não permissão para decidir:

| Item | Local com o marcador | ADR |
|---|---|---|
| Mecanismo de temporização | Doc 2, §12.2 | 0007 |
| Recurso principal declarado de localização em segundo plano | Doc 2, §34.5 (linha "o que o formulário declara") | 0008 |
| `margem_de_rede` do vigilante | Doc 2, §18.7 | **0005-B** (dono único; o ADR-0007 decide o mecanismo de temporização, e o 0005-B fecha o parâmetro com a medição resultante) |
| Limiares de bateria e de falso positivo | Doc 5, Fase 0 | 0012 |

Onde uma regra precisar de número antes do ADR, o número é **valor provisório declarado como tal**, com o ADR dono identificado. Valor provisório não é decisão fechada e não vira permanente por uso.

**Itens marcados `[PENDENTE — DECISÃO DO FUNDADOR]`** são escolhas de produto com custo de mercado ou de escopo, não de medição. O agente não as decide e não as implementa em nenhuma das opções. São três: exigência de tela de bloqueio segura e destino do PIN interno (Doc 2, §14.3); número de contatos no MVP (Doc 2, §10.2); saída para o encerramento offline (Doc 2, §10.4).

Motivo da hierarquia: agentes diferentes trabalhando em momentos diferentes precisam de uma única fonte de verdade. Divergência silenciosa é a principal causa de degradação arquitetural em desenvolvimento assistido por IA.

---

## 1. Carregamento por tarefa

| Tipo de tarefa | Carregar |
|---|---|
| Qualquer tarefa | `00-nucleo.md` (este arquivo) |
| Código Android (Compose, Room, WorkManager, localização, notificações) | `10-android.md` |
| Código backend (NestJS, API, filas, banco, vigilante, billing) | `20-backend.md` |
| Qualquer código que toque autenticação, localização, contatos, logs, criptografia, notificações | `30-seguranca.md`, além do módulo da plataforma |
| Escrever ou alterar testes; discutir cobertura | `40-qualidade.md` |
| Branch, commit, PR, review, release, ADR, versionamento | `50-processo.md` |
| Dúvida de escopo, ordem, critério de aceite ou evidência | **Documento 5, fase pertinente** |
| Dúvida de arquitetura, estados, temporização, identidade, chaves | **Documento 2, seção pertinente** |
| Dúvida de requisito de produto | Documento 1, seção pertinente |
| Dúvida de ameaça, privacidade ou LGPD | Documento 3, seção pertinente |

Não carregar tudo de uma vez. Documento longo demais no contexto reduz a aderência às regras.

---

## 2. Filosofia de engenharia

1. **Correto antes de completo, completo antes de rápido.** Um recurso de segurança errado é pior que um recurso ausente: cria falsa sensação de proteção (Doc 1, §27.15).
2. **Local first, com duas garantias distintas** (Doc 2, §4.5).
   - *Garantia local:* ativar, registrar, temporizar e sobreviver a reinício **nunca dependem de rede**.
   - *Garantia externa:* alguém fora do aparelho descobrir que o usuário parou de responder **depende do backend**, sempre.
   Nenhum código, texto de interface ou material de comunicação pode sugerir que a segunda existe sem a primeira ter alcançado o servidor. O estado de cobertura (Doc 2, §11.1) é obrigatório na interface.
3. **Fail-safe.** Nenhuma falha, timeout ou ausência de resposta dispara ação destrutiva ou irreversível. Em dúvida, o sistema degrada, não destrói.
4. **Segurança e privacidade são requisitos funcionais.** Não são camada final nem "hardening depois". Uma feature sem os controles do Doc 3 está incompleta, não "quase pronta".
5. **Best-effort declarado.** Push, GPS, background e rede são canais sem garantia. O código e a interface nunca prometem o que a plataforma não garante.
6. **Tudo que é crítico é testável.** Relógio, rede, localização, notificações e billing ficam atrás de interfaces substituíveis por fakes.
7. **Simplicidade deliberada (KISS/YAGNI).** A solução mais simples que atende o requisito e as regras de segurança. YAGNI vale para abstrações e features especulativas; nunca vale para controle de segurança já especificado.
8. **DRY com juízo.** Duplicação de conhecimento (regra de negócio em dois lugares) é proibida. Duplicação acidental de código trivial é preferível a uma abstração errada.
9. **Rastreabilidade.** Evento crítico é registro imutável; decisão arquitetural é ADR; mudança de banco é migration versionada.
10. **Honestidade técnica.** Limitação declarada vale mais que demo que finge funcionar. Este princípio gera a Regra Máxima abaixo.

---

## 3. A Regra Máxima

> **Nenhuma funcionalidade crítica é considerada pronta porque o código compila, porque os testes passam no emulador ou porque a tela renderiza.**

**Funcionalidade crítica** é qualquer código que toque: máquinas de estado do Modo Rua e do protocolo; check-in e temporização; vigilante e escalonamento; localização; sincronização de eventos; alertas e notificações; contatos de confiança; autenticação, sessão e recuperação; criptografia e Keystore; billing; exclusão de dados; job de purga local e de retenção.

### 3.1 Dois níveis de evidência

| Nível | Quando | Conteúdo obrigatório |
|---|---|---|
| **Evidência unitária** | **Toda PR** de funcionalidade crítica | testes unitários das regras de domínio; testes de integração dos componentes envolvidos; instrumentados em dispositivo gerenciado quando envolver Android; seção "Limitações e riscos" preenchida; impacto de segurança avaliado contra o Doc 3; documentação correspondente atualizada |
| **Evidência de campo** | Por **marco de fase** e sempre que a mudança **alterar comportamento observável** de background, localização, notificação, Keystore, boot ou adaptação de fabricante | template do §9, executado por humano em aparelho físico da matriz aplicável, com impacto de bateria observado |

**O rótulo é derivado dos caminhos alterados, não do julgamento do autor.** Qualquer diff que toque `core:location`, `core:notifications`, `core:security`, `sync/workers`, `BootReceiver`, `AndroidManifest.xml`, declarações de serviço em primeiro plano ou os arquivos de política de backup recebe `aguardando-evidencia-campo` **automaticamente**. O humano só pode **remover** o rótulo, com justificativa escrita na PR, que fica registrada. **Resíduo declarado, em vez de simulado:** a aplicação do rótulo é verificável por máquina; a **remoção** é julgamento humano e não é verificável por máquina. Com uma pessoa na operação não há segundo revisor para conferi-la — a limitação é declarada aqui, como o Documento 3 §37.3 declara a ausência de segregação de funções. Motivo: a versão anterior classificava a exigência de evidência de campo como gate automatizado quando o que era automatizável era apenas a presença do rótulo — e, pela régua deste documento ("regra que não falha o build é sugestão"), o controle mais importante daqui era uma sugestão.

**"Alterar comportamento observável"** significa mudar agendamento, permissão solicitada, canal de notificação, tipo de serviço, chave, política de retry, texto de notificação ou comportamento pós-boot. Refatoração interna, renomeação, ajuste de teste e mudança visual que não afetem nada disso **não** disparam a exigência. A PR declara explicitamente qual dos dois casos se aplica, e o revisor confirma.

### 3.2 Estado da entrega

Enquanto faltar qualquer item do nível aplicável, o status obrigatório é **NÃO IMPLEMENTADA**, escrito explicitamente na entrega e no changelog, mesmo que o código exista e funcione. PR aguardando campo fica com o rótulo `aguardando-evidencia-campo` e não faz merge.

Marcar como pronta uma funcionalidade sem evidência é a violação mais grave deste documento.

Motivo: LLMs têm viés de declarar sucesso. Este produto lida com pessoas em situação de roubo e coação; uma proteção que só funciona no emulador é dano real.

---

## 4. Fluxo obrigatório de trabalho do agente

**Antes de escrever qualquer código:**

1. Ler a tarefa, identificar o tipo (§1) e carregar os módulos correspondentes.
2. Identificar a fase do Documento 5 e confirmar que o item pertence a ela. Item de fase futura não se implementa "por adiantamento".
3. Verificar a Definition of Ready (§7). Se não atendida, devolver a tarefa com o que falta.
4. Produzir um plano curto: arquivos que serão tocados, contratos afetados, testes que serão escritos, riscos, seções dos Documentos 1, 2, 3 e 5 consultadas.
5. Se o plano exigir desvio de qualquer documento, ou tocar item `[ABERTO — FASE 0]`: parar e propor ADR antes de codar.

**Durante:**

6. Afirmações sobre comportamento de plataforma (Android, Play, FCM, Billing, PostgreSQL, NestJS) devem se apoiar em documentação oficial. Se o agente não tem certeza e não pode verificar, escreve `VERIFICAR:` com a dúvida exata em vez de assumir.
7. Nenhuma API, parâmetro, permissão ou comportamento inventado. Em caso de dúvida entre duas APIs, escolher a documentada e registrar a alternativa.
8. Commits atômicos com mensagem convencional (módulo 50).

**Ao entregar:**

9. Rodar a Definition of Done (§8).
10. Entregar junto: decisões tomadas, trade-offs, alternativas rejeitadas, limitações conhecidas e o que **não** foi feito.
11. Nunca descrever a entrega como mais completa do que ela é.

---

## 5. Regras específicas para agentes de IA

Os agentes DEVEM:

- consultar documentação oficial antes de afirmar comportamento de plataforma;
- justificar tecnicamente decisões não triviais;
- declarar incerteza com `VERIFICAR:` em vez de escolher silenciosamente;
- registrar limitações, decisões e trade-offs em toda entrega crítica;
- tratar os cenários do Doc 3 como casos de teste, não como teoria;
- preferir código explícito e legível a código "esperto": o próximo leitor será outro agente sem o contexto desta sessão.

Os agentes NUNCA:

- **recebem dado de produção no contexto.** Nenhum log, dump, payload, captura de tela, e-mail de usuário, coordenada ou identificador real entra em prompt, chat, issue ou arquivo de trabalho. Dados sintéticos, sempre. Um log com coordenadas colado em ferramenta externa é incidente de dados pessoais com dever de registro (Doc 3, §39.3);
- **recebem credencial de produção.** Ambientes de execução de agente acessam repositório e ambientes descartáveis, nunca segredos reais;
- inventam API, permissão, política de loja ou comportamento do Android;
- simulam sucesso: sem retornos fictícios, sem dados hardcoded fingindo ser reais, sem tela que aparenta funcionar sem implementação por trás. Mock e fake só em código de teste ou atrás de flag explicitamente marcada `FAKE_` com issue aberta;
- reduzem segurança para "fazer funcionar": nada de `cleartextTrafficPermitted`, validação de certificado ignorada, autorização comentada, redaction desligada, criptografia removida "temporariamente";
- criam mock de componente de segurança (auth, crypto, autorização) fora de teste;
- desabilitam, excluem ou marcam `@Ignore` em teste para fazer o CI passar;
- resolvem conflito de estado de emergência com last-write-wins;
- fecham item `[ABERTO — FASE 0]` sem ADR;
- implementam funcionalidade listada como fora do MVP (Doc 1, §21.3) — nem como "esqueleto para depois" — sem instrução explícita do fundador e ADR;
- implementam qualquer comportamento oculto ao usuário do aparelho: sem modo invisível, sem coleta silenciosa, sem exceções;
- ocultam bug encontrado durante a tarefa: bug achado vira issue, mesmo que fora do escopo;
- criam `TODO` sem issue associada (formato `TODO(#123): descrição`);
- removem código, teste ou verificação existente sem justificativa escrita na PR.

---

## 6. Proibições absolutas

Consolidação executiva. Detalhes e motivos nos módulos e nos Documentos 2 (§41) e 3 (§52).

**Integridade do trabalho**
- Declarar pronta funcionalidade crítica sem o pacote de evidências aplicável (§3).
- Fingir que teste manual foi feito. Evidência inventada encerra a validade de toda a entrega.
- Entregar fluxo incompleto descrito como completo.

**Dados e agentes**
- Dado de produção ou credencial de produção em contexto de agente, em qualquer forma.

**Código**
- `Thread.sleep` ou espera cega em código de produção; `runBlocking` fora de main e testes.
- Cronômetro crítico apenas em memória: prazos são persistidos com as três bases de tempo do Doc 2, §12.3.
- **Ação externa ou irreversível decidida apenas pelo relógio do aparelho.** Temporização local é legítima; a autoridade sobre prazo vencido para efeito externo é do servidor.
- Capturar exceção e engolir (`catch` vazio ou só log) em fluxo crítico.
- `!!` em código de produção sem justificativa em comentário.
- Warning crítico de lint ou Detekt suprimido sem justificativa e issue.

**Segurança**
- Segredo, chave, token ou senha em código, Git, log ou artefato de build.
- Localização precisa, PIN, senha, token ou payload sensível em log, URL, push, **SMS**, analytics ou crash report.
- Criptografia própria; primitivas fora da lista aprovada (módulo 30).
- PIN ou senha em texto puro em qualquer armazenamento; PIN usado como chave; **segredo do aplicativo usado para desbloquear chave do Keystore** (Doc 2, §14.3).
- **Chave que exige autenticação de usuário protegendo dado que precisa ser lido por worker** — a fila pendente fica sob `K_dados`.
- **Janela de validade de autenticação em `K_leitura` ou `K_confirmacao`**: autenticação a cada uso.
- SMS como único fator de **autenticação**. SMS como canal de alerta ao contato é obrigatório e não conflita com esta regra.
- Contato de confiança com acesso permanente, poderes de administrador ou capacidade de alterar estado de protocolo.

**Android**
- Accessibility Service para controlar ou observar outros apps.
- Pedir todas as permissões no onboarding; permissão fora de contexto.
- Foreground service fora de fluxo iniciado e permitido pelo usuário.
- Instruir o usuário a desativar toda a proteção de bateria do fabricante.
- Iniciar sessão sem permissão de notificação e canal `check_in` ativos.

**Backend e dados**
- Alterar schema sem migration versionada; migration destrutiva sem expand-contract.
- Endpoint mutante sem idempotência; consumidor de fila não idempotente.
- Efeito colateral fora do padrão outbox quando a transação exigir notificação.
- Ação destrutiva automática por timeout, heartbeat ausente ou falta de resposta.
- Índice único global em tabela destinada a particionamento (usar `event_dedup`, Doc 2 §19.5).
- **Endpoint mutante sem linha na tabela de idempotência** (Doc 2, §16.3). A tabela cobre todos; ausência de linha é bug da tabela, não licença.
- **Aceitar do aparelho prazo ou `grace_seconds` sem o limite de servidor** do Doc 2, §18.7, item 2a.
- **Aceitar confirmação de check-in sem verificar a assinatura** (Doc 2, §16.8), ou tratar `confirmation_type` como declaração do cliente.

**Processo**
- Alterar arquitetura, dependência estrutural ou contrato público sem ADR.
- Adicionar biblioteca sem passar pelos critérios do módulo 50.
- Deploy direto a 100% de usuários; release sem caminho de rollback.

---

## 7. Definition of Ready

Uma tarefa só começa quando:

- [ ] o objetivo está descrito em uma frase verificável ("o usuário consegue X", "o sistema registra Y");
- [ ] o tipo da tarefa e os módulos aplicáveis foram identificados;
- [ ] a fase do Documento 5 foi identificada e o item pertence a ela;
- [ ] critérios de aceite estão listados, incluindo cenários offline e de falha quando aplicável;
- [ ] dependências de outras tarefas ou decisões estão resolvidas ou explicitadas;
- [ ] se tocar funcionalidade crítica: as seções relevantes dos Documentos 2 e 3 foram identificadas;
- [ ] se depender de item `[ABERTO — FASE 0]` ou exigir decisão arquitetural nova: ADR proposto antes do código.

Se algo faltar, o agente devolve a tarefa listando exatamente o que falta.

---

## 8. Definition of Done

Uma tarefa só termina quando:

- [ ] critérios de aceite atendidos e demonstráveis;
- [ ] regras dos módulos aplicáveis respeitadas, com o checklist do módulo na PR;
- [ ] testes novos escritos e todos os existentes passando; nenhum teste desabilitado;
- [ ] lint, Detekt e ktlint (Android) ou lint e typecheck (backend) sem novos warnings;
- [ ] gates de CI (§11) verdes;
- [ ] logs revisados: nada da lista proibida (módulo 30, §2);
- [ ] migrations com rollback definido, quando houver;
- [ ] documentação e OpenAPI atualizados, quando houver mudança de contrato;
- [ ] PR criada com o template do módulo 50, incluindo "Limitações e riscos";
- [ ] nível de evidência aplicável declarado e cumprido (§3.1). Se for campo e ainda não houve, a PR fica `aguardando-evidencia-campo` e a funcionalidade permanece NÃO IMPLEMENTADA.

---

## 9. Template único de evidência

Canônico para o projeto. O Documento 5 remete a este template e não mantém outro. Colar preenchido na PR e arquivar em `docs/evidencias/`.

```markdown
## Evidência de teste

- Nível: (unitária | campo)
- Fase e item do Documento 5:
- Funcionalidade:
- Status declarado: (IMPLEMENTADA | NÃO IMPLEMENTADA)
- Issue / PR / Commit / Versão / Ambiente:
- Executor: (humano identificado; o agente não executa teste físico e não preenche resultado observado)
- Data e hora:

### Aparelho (nível campo)
- Fabricante / Modelo
- Android e skin (ex.: One UI 6)
- Bateria e otimização ativa
- Permissões concedidas
- Estado de rede (Wi-Fi / dados / offline / alternância)

### Cenário executado
1.
2.

### Resultado esperado
### Resultado observado
### Divergências

### Testes automatizados
- Unitário / Integração / Instrumentado / E2E

### Impactos
- Segurança (avaliado contra Doc 3, seções ...)
- Privacidade / Bateria / Desempenho / Rede

### Limitações conhecidas
### Pendências (issues criadas)

### Decisão
- [ ] Aceita
- [ ] Aguardando validação
- [ ] Bloqueada
- [ ] Redesenho necessário
```

---

## 10. ADR em dois níveis

| Instrumento | Quando | Aprovação |
|---|---|---|
| **ADR completo** | Lista do Documento 2, §39; qualquer desvio dos Documentos 2, 3, 4 ou 5; contrato público; schema de evento; mudança de SDK; canal de notificação novo; pinning; dependência estrutural; limiares de performance | Fundador, explícita. **Segurança nunca é aprovada por silêncio** |
| **Decision Log** | Escolhas de baixo risco: nomes, organização de pacote, biblioteca utilitária sem superfície de segurança, limiar de interface | Registro curto na mesma pasta, revisado em lote semanal |

Áreas em que só existe ADR completo: autenticação, criptografia, localização, billing, máquinas de estado, vigilante, migrations, retenção, permissões e políticas de loja.

Local: `docs/adr/NNNN-titulo.md`, numeração sequencial, modelo do Documento 2, §39. Agente pode **propor** ADR; só o fundador aceita. ADR é imutável depois de aceito; mudança de decisão é ADR novo que referencia e substitui o anterior.

---

## 11. Gates automatizados de CI

Regra que não falha o build é sugestão. Implantados antes da primeira linha de código de produção.

| Gate | Verifica |
|---|---|
| Secrets scanning | Segredo em qualquer arquivo do repositório |
| Grafo de módulos (Gradle) | Dependências proibidas do módulo 10, §1; `domain:*` sem Android |
| Detekt customizado | Relógio direto, `GlobalScope`, `runBlocking`, `!!`, `Thread.sleep`, `catch` genérico em pacotes críticos, chamada de log recebendo tipo marcado como sensível |
| API proibida | Lista fechada de criptografia (módulo 30, §3); `fallbackToDestructiveMigration`; `cleartextTrafficPermitted` |
| Testes | Nenhum `@Ignore` nem teste desabilitado; pisos de cobertura do módulo 40, §3 |
| Autorização | Todo endpoint com identificador de recurso possui teste negativo correspondente |
| Marcadores | `VERIFICAR:` em código de produção falha o build; `TODO` sem issue válida falha o build |
| Evidência | `CODEOWNERS` em `docs/evidencias/` e nas áreas críticas; rótulo `aguardando-evidencia-campo` bloqueia merge |
| Migrations | Executadas em banco efêmero; rollback declarado |
| Listas canônicas | Todo bloco marcado `<!-- gerado de X -->` é regenerado da origem X e comparado; divergência falha o build. A lista de origens é derivada dos marcadores presentes no corpus, **não** de uma lista fixa no gate — hoje: Doc 2 §12.3, Doc 2 §18, Doc 3 §34.1, Doc 3 §20.3. Lista canônica sem cópia (Doc 3, §51, apenas remetida) não entra no gate, e **nenhum texto pode afirmar validação em CI sem carregar o marcador** |
| Marcadores abertos | Cada item das tabelas do §0 tem **ao menos uma** ocorrência do marcador na seção citada; falta de ocorrência falha o build. Ocorrências em outras seções são **permitidas** e devem citar o item dono. A cadeia literal conferida é `[ABERTO — FASE 0]` e `[PENDENTE — DECISÃO DO FUNDADOR]`, com travessão, não hífen. Texto que *fala sobre* o marcador fica fora da contagem por lista de exceções explícita no próprio gate |
| Idempotência | Endpoint mutante novo sem linha na tabela do Doc 2, §16.3 |
| Backup | Manifesto com `dataExtractionRules` sem `fullBackupContent`, ou sem `<device-transfer>`, falha o build |
| `modo_teste` | Invariante de estrutura, não análise de fluxo: existe **um único ponto de despacho tipado** para cada provedor de notificação (SMS, push, e-mail), e o gate falha se qualquer outro call site chamar o provedor diretamente. A flag é parâmetro obrigatório da assinatura desse ponto, e o compilador cobra |
| Rótulo de evidência | `aguardando-evidencia-campo` aplicado automaticamente por caminho alterado (§3.1); remoção exige justificativa registrada |

Nenhum gate é desativado sem ADR e issue de reativação.

---

## 12. Mapa dos módulos

| Arquivo | Conteúdo |
|---|---|
| `10-android.md` | Estrutura de módulos, Kotlin, Compose, máquina de estados local, tempo, background, localização, armazenamento e chaves, notificações, bateria, compatibilidade |
| `20-backend.md` | Módulos NestJS, API, eventos e idempotência, vigilante, outbox, filas, banco e migrations, autorização, billing, observabilidade |
| `30-seguranca.md` | Segredos, logs, criptografia aprovada, autenticação, dados de localização, anti-stalking, agentes de IA, bloqueadores de release |
| `40-qualidade.md` | Pirâmide de testes, determinismo, cobertura, matriz de aparelhos, resiliência, testes do vigilante, benchmarks, gates |
| `50-processo.md` | Branches, commits, PR, code review, ADR e Decision Log, versionamento, release e rollback, feature flags, dependências, documentação |
