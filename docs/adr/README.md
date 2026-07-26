# ADRs — Decisões arquiteturais

Local canônico: `docs/adr/NNNN-titulo.md`, numeração sequencial.
Modelo obrigatório (Documento 2, §39): **contexto; opções consideradas; decisão;
consequências positivas; consequências negativas; evidências; data de revisão.**

**Agente propõe. Só o fundador aceita.** Segurança nunca é aprovada por silêncio — não existe
prazo cujo vencimento autorize seguir sem resposta (mód. 50 §5).
ADR aceito é **imutável**; mudança de decisão é ADR novo que referencia e substitui o anterior.

Itens `[PENDENTE — DECISÃO DO FUNDADOR]` **não se resolvem por ADR proposto por agente**: são
decisões de produto com custo de mercado ou de escopo.

## Estados

| Estado | Significado |
|---|---|
| `PROPOSTO` | Escrito por agente, aguardando aceite do fundador. **Não vale como decisão.** |
| `ACEITO` | Aceito pelo fundador, com data. Imutável a partir daí. |
| `SUBSTITUÍDO por NNNN` | Decisão revista por ADR posterior |
| `REJEITADO` | Proposta recusada, mantida como registro |

## Situação

| ADR | Assunto | Prazo (Doc 2 §39) | Arquivo | Estado |
|---|---|---|---|---|
| 0001 | Kotlin nativo, Compose, monólito modular, PostgreSQL | Antes da Fase 0 | `0001-stack-base.md` | **PROPOSTO** |
| 0002 | `minSdk` 30 provisório e `targetSdk` 36 | Antes da Fase 0 | `0002-minsdk-targetsdk.md` | **PROPOSTO** — um critério de dois satisfeito, ver §Lacuna |
| 0003 | Estrutura descartável da Fase 0 | Antes da Fase 0 | `0003-estrutura-descartavel-fase-0.md` | **PROPOSTO** |
| 0004 | Hierarquia de chaves locais e política de backup | Antes da Fase 1 | — | não escrito |
| 0005-A | Parâmetros do vigilante que nascem provisórios | Antes da Fase 1 | — | não escrito |
| 0005-B | Parâmetros que dependem de medição (`margem_de_rede`) | Após a Fase 0 | — | não escrito |
| 0006 | Autenticação, sessão própria e step-up | Antes da Fase 2 | — | não escrito |
| 0007 | **Arquitetura de temporização** | Após a medição da Fase 0 | — | não escrito |
| 0008 | Recurso principal declarado de localização em segundo plano | Após o teste de listagem da Fase 0 | — | não escrito |
| 0009 | Particionamento, retenção física e idade máxima de evento | Antes do beta, ou logo após o teste de carga da Fase 2 | — | não escrito |
| 0010 | RLS: mecanismo completo ou remoção | Antes da Fase 2 | — | não escrito |
| 0011 | Provedor de SMS e política de cascata | **Provisório na Fase 0**; definitivo antes da Fase 3 | — | não escrito |
| 0012 | Limiares de bateria e falso positivo | Após a Fase 0, revisto após a Fase 6 | — | não escrito |
| 0013 | Mecanismo de prova de autenticação da confirmação | Antes da Fase 1 | — | não escrito |

O Documento 2 §39 lista o 0013 antes do 0012, quebrando a ordem sequencial da própria tabela
(registrado em `docs/consistencia/backlog.md`, item E9). Esta tabela usa a ordem numérica.

## Ordens impostas entre ADRs

- **O parecer de classificação como aplicativo de monitoramento precede o ADR-0007.** A política
  de aplicativos de monitoramento exige notificação persistente durante toda a execução mais
  ícone único — o que pode eliminar candidato de temporização **por política**, não por medição
  (Documento 2, §12.2 e §34.6).
- **ADR-0005-B é o dono único da `margem_de_rede`** (correção ARB3-003).
- **ADR-0013 precisa incorporar as correções ARB4-001 e ARB4-002** antes de ser proposto
  (ARB4, Decisão B).
