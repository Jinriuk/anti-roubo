# Modo Rua — instruções para agentes

Este arquivo **roteia**. Ele não é fonte de arquitetura, de ameaça, de escopo, de fase, de
critério de aceite nem de evidência. Onde ele parecer contradizer um documento, o documento
vence e a divergência vira issue.

> ⚠️ **Este projeto opera hoje sem `docs/agentes/00-nucleo.md`.**
> Leia `docs/agentes/00-nucleo.AUSENTE.md` **antes de qualquer tarefa**. Ele diz o que falta,
> o que depende do que falta e o que você não pode concluir enquanto faltar.

---

## 1. Hierarquia de autoridade

Em conflito, vale nesta ordem. Nenhuma decisão pode contrariar um nível superior.

```text
Lei (LGPD / ANPD)
  └─ Políticas da Google Play
       └─ Documento 3 — Segurança .................. docs/03-seguranca.md
            └─ Documento 5 — Roadmap ............... docs/05-roadmap.md
                 └─ Documento 2 — Arquitetura ...... docs/02-arquitetura.md
                      └─ Documento 4 — Regras ...... docs/agentes/
                           └─ ADRs ................. docs/adr/
                                └─ Código
```

`docs/01-visao.md` (Documento 1) declara-se nível 6. A conciliação entre os níveis numerados
dos cabeçalhos e esta ordem depende da tabela do núcleo §0, que não está disponível — ver o
arquivo de ausência.

## 2. O que ler antes de cada tarefa

| Você vai mexer em | Leia |
|---|---|
| Qualquer coisa | a fase corrente em `docs/05-roadmap.md` (escopo, critério de aceite, evidência) |
| `android/` | `docs/agentes/10-android.md` + Documento 2 §8–§17 e §35 |
| `backend/`, painel consumindo a API | `docs/agentes/20-backend.md` + Documento 2 §18–§27 |
| Dado sensível, auth, localização, chave, log, notificação, contato | `docs/agentes/30-seguranca.md` + o Documento 3 na seção pertinente |
| Teste, cobertura, evidência | `docs/agentes/40-qualidade.md` |
| Branch, commit, PR, review, ADR, release | `docs/agentes/50-processo.md` |
| `spike/` (Fase 0) | `docs/adr/0003-estrutura-descartavel-fase-0.md` + Documento 5, Fase 0 |

A tabela definitiva de módulo × tarefa é o núcleo §1. Esta é uma aproximação derivada dos
cabeçalhos dos próprios módulos, e está declarada como tal.

## 3. Regras que param o trabalho

Estas não são resumo das regras do projeto — são os quatro gatilhos de **parada**. As regras
completas estão nos documentos.

1. **`[ABERTO — FASE 0]`** — não decida. Pare e **proponha ADR** com base em medição.
   Fechar um desses sem ADR é bloqueador de release (Documento 3, §51).
2. **`[PENDENTE — DECISÃO DO FUNDADOR]`** — não decida **e não implemente nenhuma das
   opções**. Apresente opções e custos, e pare. São três, listadas em
   `docs/consistencia/backlog.md`.
3. **Conflito entre documentos, ou regra obsoleta** — não corrija em silêncio e não ignore.
   **Abra issue** apontando qual dos dois está errado.
4. **Afirmação sobre plataforma Android ou política da Play** — exige fonte oficial citada,
   **inclusive quando a afirmação for mais restritiva que a fonte**. Na dúvida, escreva
   `VERIFICAR:` com a pergunta exata. `VERIFICAR:` em código de produção falha o build;
   em documentação é legítimo.

## 4. Limites do agente

- Nenhum dado de produção entra no contexto de um agente, em nenhuma forma — log, dump,
  payload, captura, e-mail, telefone, coordenada, identificador real. Dados sintéticos,
  sempre. Violação é incidente de dados pessoais SEV-1 (Documento 3, §39.3).
- Nenhuma credencial de produção é acessível a ferramenta agêntica.
- **Agente propõe ADR; só o fundador aceita.** Segurança nunca é aprovada por silêncio.
- Nada é considerado pronto porque compila. Uma fase termina quando o comportamento foi
  comprovado — e evidência inventada é falha crítica.

## 5. Mapa do repositório

```text
docs/
├── 01-visao.md · 02-arquitetura.md · 03-seguranca.md · 05-roadmap.md
├── agentes/       Documento 4: README + núcleo (ausente) + módulos 10 a 50
├── adr/           decisões arquiteturais — índice em adr/README.md
├── fase-0/        método de medição e plano da Fase 0
├── fabricantes/   comportamento observado por marca (produto da Fase 0)
├── evidencias/    evidências de teste manual (produto de todas as fases)
├── auditoria/     changelogs e relatórios do Verification Board (não normativos)
└── consistencia/  backlog de divergências entre documentos
```

Estado atual: **nenhum código existe**. Não há `android/`, `backend/` nem `spike/`.
A Fase 0 não iniciou formalmente — suas pré-condições estão em `docs/fase-0/README.md`.
