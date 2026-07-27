# Modo Rua — instruções para agentes

Este arquivo **roteia**. A fonte é `docs/agentes/00-nucleo.md`, que deve estar **sempre** no
contexto. Onde este arquivo parecer contradizer o núcleo ou qualquer documento, o documento
vence e a divergência vira issue.

---

## 1. Hierarquia de autoridade

**Fonte: núcleo §0.** Em qualquer conflito, vence a ordem:

```text
1. Lei (LGPD) e políticas da Google Play
2. Documento 3 — Segurança ................ docs/03-seguranca.md
3. Documento 4 — Regras de Engenharia ..... docs/agentes/
4. Documento 5 — Roadmap .................. docs/05-roadmap.md
5. Documento 2 — Arquitetura .............. docs/02-arquitetura.md
6. Documento 1 — Visão do Produto ......... docs/01-visao.md
7. Instruções da tarefa atual
```

> ⚠️ **O Documento 4 está no nível 3 — acima dos Documentos 5, 2 e 1.** Isso é contraintuitivo
> e tem uma cláusula que o limita, no mesmo §0:
>
> **O Documento 4 não é fonte de conteúdo arquitetural, de estados, de contratos, de modelo de
> dados, de ameaças, nem de escopo, fase, critério de aceite ou evidência.** Onde um módulo
> reenunciar conteúdo dos Documentos 2, 3 ou 5, **prevalece a fonte**, e a reenunciação
> divergente é **bug a corrigir** — não conflito a resolver por hierarquia.

Se a tarefa contrariar um nível superior, o agente **para, reporta e aguarda decisão**. Não
implementa "do jeito pedido" nem corrige silenciosamente "do jeito certo". Exceção só por ADR
aprovado pelo fundador.

## 2. O que carregar por tarefa

**Fonte: núcleo §1.** Não carregar tudo de uma vez — contexto inflado reduz aderência às regras.

| Tipo de tarefa | Carregar |
|---|---|
| Qualquer tarefa | `docs/agentes/00-nucleo.md` |
| Código Android | `10-android.md` |
| Código backend | `20-backend.md` |
| Qualquer código que toque autenticação, localização, contatos, logs, criptografia, notificações | `30-seguranca.md`, **além** do módulo da plataforma |
| Testes e cobertura | `40-qualidade.md` |
| Branch, commit, PR, review, release, ADR, versionamento | `50-processo.md` |
| Dúvida de escopo, ordem, critério de aceite ou evidência | **Documento 5**, fase pertinente |
| Dúvida de arquitetura, estados, temporização, identidade, chaves | **Documento 2**, seção pertinente |
| Dúvida de requisito de produto | Documento 1 |
| Dúvida de ameaça, privacidade ou LGPD | Documento 3 |
| `spike/` (Fase 0) | `docs/adr/0003-estrutura-descartavel-fase-0.md` + Documento 5, Fase 0 |

## 3. Regras que param o trabalho

Os gatilhos de **parada**. As regras completas estão no núcleo §§4 a 6.

1. **`[ABERTO — FASE 0]`** — não decida. Pare e **proponha ADR**. São quatro, com local exato na
   tabela do núcleo §0. Fechar sem ADR é violação grave e bloqueador de release.
2. **`[PENDENTE — DECISÃO DO FUNDADOR]`** — não decida **e não implemente nenhuma das opções**.
   Apresente opções e custos, e pare. São três, na tabela do núcleo §0.
3. **Conflito entre documentos, ou reenunciação divergente** — não corrija em silêncio e não
   ignore. **Abra issue.** Backlog em `docs/consistencia/backlog.md`.
4. **Afirmação sobre plataforma Android ou política da Play** — exige fonte oficial,
   **inclusive quando for mais restritiva que a fonte**. Na dúvida, `VERIFICAR:` com a pergunta
   exata. `VERIFICAR:` em código de produção falha o build; em documentação é legítimo.

## 4. A Regra Máxima

> **Nenhuma funcionalidade crítica é considerada pronta porque o código compila, porque os
> testes passam no emulador ou porque a tela renderiza.** (núcleo §3)

**Dois níveis de evidência** (núcleo §3.1): unitária em **toda PR** de funcionalidade crítica;
de campo **por marco de fase** e sempre que a mudança alterar comportamento observável de
background, localização, notificação, Keystore, boot ou adaptação de fabricante.

O rótulo `aguardando-evidencia-campo` é **derivado dos caminhos alterados**, não do julgamento
do autor. O agente só pode removê-lo com justificativa escrita na PR. Enquanto faltar evidência,
o status obrigatório é **NÃO IMPLEMENTADA**, escrito explicitamente — mesmo que o código exista
e funcione.

Template de evidência: **núcleo §9** (canônico). Colar preenchido na PR e arquivar em
`docs/evidencias/`.

**O agente não executa teste físico e não preenche resultado observado** (núcleo §9). Ele
escreve o método, o instrumento e todos os testes automatizáveis; a coleta em aparelho é de
executor humano identificado.

## 5. Limites do agente

Núcleo §5 e §6, resumidos aqui apenas como lembrete — a lista completa está lá:

- Nenhum dado de produção no contexto, em nenhuma forma. Nenhuma credencial de produção.
- Não inventar API, permissão, política de loja ou comportamento do Android.
- Não simular sucesso: sem retorno fictício, sem dado hardcoded fingindo ser real.
- Não reduzir segurança para "fazer funcionar".
- Não desabilitar nem `@Ignore` teste para o CI passar.
- Bug encontrado vira issue, mesmo fora do escopo.
- **Agente propõe ADR; só o fundador aceita.** Segurança nunca é aprovada por silêncio.

## 6. Mapa do repositório

```text
docs/
├── 01-visao.md · 02-arquitetura.md · 03-seguranca.md · 05-roadmap.md
├── agentes/       Documento 4: núcleo (sempre no contexto) + módulos 10 a 50
├── adr/           decisões arquiteturais — índice em adr/README.md
├── fase-0/        método de medição e situação da Fase 0
├── fabricantes/   comportamento observado por marca (produto da Fase 0)
├── evidencias/    evidências de teste (template no núcleo §9)
├── auditoria/     changelogs e relatórios do Verification Board (não normativos)
└── consistencia/  backlog de divergências entre documentos
```

Estado atual: **nenhum código existe**. Não há `android/`, `backend/` nem `spike/`.
Situação e pré-condições da Fase 0 em `docs/fase-0/README.md`.
