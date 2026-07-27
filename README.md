# Modo Rua

Plataforma móvel de proteção **pós-roubo**, com prioridade para Android e lançamento inicial no
Rio de Janeiro.

> **Proteger a vida digital, financeira e pessoal do usuário quando ele perde o controle físico
> do celular.**

O produto não impede o roubo e não promete recuperar o aparelho. Ele reduz o intervalo entre o
momento em que o usuário perde o controle do celular e o momento em que ele e sua rede de
confiança conseguem agir.

## A decisão que organiza todas as outras

> **O aparelho é a autoridade sobre a sessão. O servidor é a autoridade sobre a emergência.
> O aparelho registra a ausência; o servidor decide o que ela significa.**

E a consequência que atravessa o produto inteiro:

> **Alertar não é uma capacidade do aparelho.** É uma capacidade do sistema, e depende de o
> servidor ter recebido a sessão antes de o aparelho desaparecer. Por isso o estado de cobertura
> é declarado ao usuário na interface, em vez de escondido.

## Estado do projeto

| | |
|---|---|
| **Fase corrente** | Fase 0 — **pronta para iniciar**, faltando revisão do método e timebox. Ver `docs/fase-0/README.md` |
| **Código de produção** | nenhum. Não existe `android/`, `backend/` nem `spike/` |
| **Documentação** | corpus completo no repositório, incluindo o núcleo v2.3 |
| **ADRs** | 0001, 0002 e 0003 ✅ **aceitos**. 0004, 0005-A e 0013 pendentes (pré-condição da Fase 1) |
| **Portão da ARB4** | ✅ os 6 itens aplicados — ver `docs/auditoria/CHANGELOG-ARB4.md` |

## Documentação

Toda a documentação vive em `docs/` e muda por PR mais ADR, como qualquer código.

| Caminho | O que é |
|---|---|
| `docs/01-visao.md` | Documento 1 — Visão do Produto e Modelo de Negócio |
| `docs/02-arquitetura.md` | Documento 2 — Arquitetura Técnica |
| `docs/03-seguranca.md` | Documento 3 — Segurança, Privacidade e Ameaças |
| `docs/05-roadmap.md` | Documento 5 — Roadmap, Critérios de Aceite e Matriz de Evidências |
| `docs/agentes/` | Documento 4 — Regras de Engenharia (**núcleo** + README + módulos 10 a 50) |
| `docs/adr/` | Decisões arquiteturais |
| `docs/fase-0/` | Método de medição e situação da Fase 0 |
| `docs/consistencia/` | Backlog de divergências entre documentos |
| `docs/auditoria/` | Changelogs e relatórios do Verification Board — **não normativos** |
| `docs/fabricantes/`, `docs/evidencias/` | Produto das fases |

**Agentes:** leiam `CLAUDE.md` antes de qualquer tarefa.

## Fora do escopo do MVP

Bloquear PIX ou bancos · esconder aplicativos · impedir desinstalação · gravação ou câmera
oculta · restauração de fábrica automática · senha de coação · comandos remotos · sinais
contextuais · ativação automática · titulares menores de 18 anos · iOS · qualquer ação
destrutiva automática.
