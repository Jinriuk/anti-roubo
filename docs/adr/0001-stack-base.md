# ADR-0001 — Kotlin nativo, Compose, monólito modular NestJS e PostgreSQL

**Estado:** **ACEITO** pelo fundador em 2026-07-27
**Data da proposta:** 2026-07-26
**Imutável a partir do aceite.** Mudança de decisão é ADR novo que referencia e substitui este (núcleo §10).
**Prazo declarado:** antes da Fase 0 (Documento 2, §39)
**Autor:** agente. Agente propõe; só o fundador aceita.

> Este ADR **não decide nada novo**. As quatro escolhas já estão no Documento 2, §4.1 a §4.4
> e §7. O §39 as lista "registradas para encerrar a fila": o valor deste documento é registrar
> as alternativas rejeitadas e o custo assumido, que hoje não existem escritos em lugar nenhum.

---

## Contexto

A arquitetura sustenta um produto que precisa continuar útil com o aparelho bloqueado, sem
rede, com processo encerrado, com notificação atrasada, sob fabricante agressivo com bateria,
com eventos duplicados ou fora de ordem, e quando o usuário perde o acesso ao aparelho
principal (Documento 2, §2). O núcleo não é um CRUD: é sistema distribuído, orientado a
eventos, offline-first e tolerante a falhas parciais, com **duas autoridades** — o aparelho
sobre a sessão, o servidor sobre a emergência (§4.5).

Condições que restringem a escolha:

- **Equipe:** o fundador mais agentes de IA. Toda complexidade operacional recai sobre uma
  pessoa (Documento 3, §37.5 — continuidade do operador é risco declarado).
- **Escala do MVP:** 20 a 50 usuários no Beta A, até 100 no Beta B, 1% de rollout na Fase 7.
  O dimensionamento sintético de 100 mil usuários é teste de carga, não carga real (Doc 2 §33).
- **Superfície de plataforma inevitável:** serviço em primeiro plano por tipo, WorkManager sob
  cota do Android 16, Keystore com autenticação por operação, `BiometricPrompt` com
  `DEVICE_CREDENTIAL`, `BOOT_COMPLETED`, geofencing, e comportamento divergente por fabricante.
- **Prioridades declaradas, nesta ordem:** confiabilidade; segurança; rastreabilidade;
  funcionamento offline do que é local; baixo consumo; privacidade; simplicidade operacional;
  evolução; testes em aparelhos reais; aderência às políticas da Play (§2).

## Opções consideradas

### Aplicativo

| Opção | Avaliação |
|---|---|
| **Kotlin nativo + Jetpack Compose** | **Escolhida.** Acesso direto às APIs, controle de ciclo de vida, previsibilidade em serviços e bateria, e capacidade de investigar falha sem atravessar camada intermediária. Compose entrega `UiState` por tela com estado degradado, previews por estado e teste de componente — todos exigidos pelo módulo 10, §3 |
| Flutter ou React Native | **Rejeitada.** Tudo o que a Fase 0 mede vive na camada de plataforma: tipo de FGS, cota de job, retomada por `BOOT_COMPLETED`, invalidação de chave, latência de geofence. Um framework híbrido acrescenta uma camada entre a medição e o fato medido, exatamente onde a hipótese crítica do produto está. O Documento 2, §4.1 já veda framework híbrido no núcleo |
| Kotlin nativo + Views XML | **Rejeitada.** Nenhum ganho. Custa os recursos de Compose que o módulo 10 §3 exige e não reduz risco algum |

### Backend

| Opção | Avaliação |
|---|---|
| **Monólito modular NestJS + TypeScript** | **Escolhida.** Separação lógica desde o início, permitindo extração posterior. NestJS impõe fronteira de módulo por construção, que é o que o módulo 20, §1 cobra ("acesso cruzado a tabela mata a opção de extração") |
| Microserviços | **Rejeitada.** Anti-padrão explícito (Documento 2, §41: "microserviços antes da necessidade"). Acrescentaria custo, complexidade, latência, superfície de falha e trabalho de infraestrutura sem benefício nesta escala — e poria um salto de rede dentro do caminho do vigilante, que tem SLO de atraso p99 ≤ 60 s |
| Funções serverless | **Rejeitada.** O vigilante exige fila com entrega retardada **mais** varredura periódica (§18.7, item 8) e o publisher do outbox exige `SELECT … FOR UPDATE SKIP LOCKED` em worker de vida longa (§20.1). Nenhum dos dois se traduz bem para execução efêmera, e o pool de conexões com PostgreSQL vira problema antes de qualquer benefício aparecer |

### Banco de dados

| Opção | Avaliação |
|---|---|
| **PostgreSQL** | **Escolhida.** Transações — o produto grava evento e estado **na mesma transação** em dois lugares críticos (§14.1 no aparelho, §20.1 no outbox); integridade referencial; JSONB; RLS (§19.2); particionamento declarativo (§19.5); PITR; maturidade |
| Firebase / Firestore | **Rejeitada.** Anti-padrão explícito (§41: "Firebase como único banco de domínio"). Sem transação multi-tabela adequada ao outbox, sem particionamento declarativo, e o modelo de autorização não sustenta o teste negativo por endpoint que o §22.3 torna obrigatório |
| MySQL | **Rejeitada.** `SKIP LOCKED` existe, mas RLS, JSONB e particionamento declarativo não têm equivalente que sustente o §19.2 e o §19.5 |

### Painel e infraestrutura

Next.js + React + TanStack Query, com **PWA** — porque o push no painel é o canal 2 da cascata
de alerta (§25.1) e depende da PWA instalada. Plataforma gerenciada, PostgreSQL e Redis
gerenciados, object storage, CDN/WAF, secret manager, provedor de SMS brasileiro, CI/CD em
GitHub Actions. A arquitetura evita dependência irreversível de um único provedor (§7).

## Decisão

Adotar exatamente a stack do Documento 2, §7:

- **Android:** Kotlin; Compose; Material 3; Navigation Compose; Coroutines; StateFlow; Hilt;
  Room; Proto DataStore; WorkManager; Retrofit ou Ktor Client; Kotlin Serialization; AndroidX
  Biometric; Android Keystore; FCM; Google Play Billing; Detekt; Ktlint; Version Catalog;
  Baseline Profiles; Macrobenchmark.
- **Backend:** Node.js LTS; TypeScript; NestJS; PostgreSQL; Redis; BullMQ ou fila gerenciada
  equivalente; Prisma ou TypeORM **com SQL explícito nas consultas críticas**; OpenAPI;
  OpenTelemetry; plataforma de erros; object storage compatível com S3; Docker.
- **Painel:** Next.js; TypeScript; React; TanStack Query; provedor de mapa abstraído; CSP
  rigorosa; PWA.
- **Play Integrity não integra o núcleo** (§37.1).

A escolha entre Prisma e TypeORM, o provedor de nuvem, a fila, o mapa, o analytics e o billing
ficam como decisões "conforme necessidade" (§39), fora deste ADR.

## Consequências positivas

- Nenhuma camada entre a medição da Fase 0 e o comportamento medido.
- O monólito modular mantém aberta a opção de extrair o vigilante, que é o componente com SLO
  próprio e o único cuja falha produz alerta falso em massa.
- PostgreSQL entrega, num só produto, as quatro propriedades que o desenho exige e que
  raramente coexistem: transação multi-tabela, RLS, JSONB e particionamento declarativo.
- Stack madura, com documentação oficial abundante — o que importa num projeto cuja regra é
  citar fonte oficial em toda afirmação de plataforma.

## Consequências negativas

Assumidas conscientemente:

- **Kotlin nativo fecha o caminho barato para iOS.** O Documento 1, §21.3 já põe iOS fora do
  MVP e o §22 o coloca como "iOS limitado" na expansão — esta decisão torna isso um reescrita,
  não uma porta.
- **Duas linguagens e dois ecossistemas de teste** (Kotlin/JUnit e TypeScript/Jest) para uma
  pessoa manter, com dois pipelines de CI, duas listas de dependência e dois relatórios de
  cobertura.
- **Custo fixo de infraestrutura antes do primeiro assinante:** PostgreSQL gerenciado, Redis
  gerenciado, object storage, CDN/WAF, secret manager, observabilidade e provedor de SMS.
- **O monólito modular só preserva a opção de extração se a regra do módulo 20 §1 for cumprida
  em todo PR.** Uma única consulta cruzando tabela de outro módulo mata a opção em silêncio,
  sem nenhum teste falhar.
- **A verificação da regra de dependência entre módulos precisa existir no Gradle** (§8) antes
  da primeira feature, não depois — senão a estrutura modular é convenção, não controle.

## Evidências

Documento 2, §2, §4.1–§4.4, §4.5, §7, §8, §14.1, §18.7, §19.2, §19.5, §20.1, §22.3, §33, §37.1,
§41. Módulo 10, §1. Módulo 20, §1. Documento 3, §37.5.

Nenhuma medição sustenta este ADR, e nenhuma é exigida por ele: as quatro escolhas são
anteriores à Fase 0 e é a Fase 0 que depende delas.

## Data de revisão

**Encerramento da Fase 2**, após o teste de carga sintético de ingestão dimensionado para
100 mil usuários. É o primeiro momento em que existe dado que pode contrariar a escolha de
monólito modular ou de PostgreSQL sem particionamento ativo.
