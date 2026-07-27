# Documento 4 — Regras de Engenharia para os Agentes
## Como instalar este conjunto nas ferramentas

**Versão:** 2.2 | **Substitui:** README 1.0, 2.0 e 2.1

---

## Estrutura

```text
docs/
├── 01-visao.md              ← Documento 1
├── 02-arquitetura.md        ← Documento 2
├── 03-seguranca.md          ← Documento 3
├── 05-roadmap.md            ← Documento 5
├── agentes/
│   ├── README.md            ← este arquivo (humano; não vai ao contexto do agente)
│   ├── 00-nucleo.md         ← sempre no contexto
│   ├── 10-android.md        ← tarefas de app Android
│   ├── 20-backend.md        ← tarefas de API, banco, filas, vigilante
│   ├── 30-seguranca.md      ← qualquer tarefa que toque dado sensível
│   ├── 40-qualidade.md      ← testes e cobertura
│   └── 50-processo.md       ← git, PR, review, ADR, release
├── adr/                     ← decisões arquiteturais (Documento 2, §39)
├── fabricantes/             ← comportamento observado por marca
└── evidencias/              ← evidências de teste manual
```

Princípio: o núcleo é curto para caber sempre; os módulos entram por tarefa. Não cole tudo de uma vez — contexto inflado reduz aderência às regras, que é exatamente o que este conjunto quer evitar.

---

## O que mudou na versão 2.0

1. **O Documento 5 entrou na hierarquia de autoridade** e passou a ser a fonte única de fases do projeto. Os agentes precisam consultá-lo para escopo, critério de aceite e evidência.
2. **O Adendo A do Documento 2 deixou de existir**: seu conteúdo foi incorporado ao Documento 2, que preservou a numeração de seções da versão 1.0. Referências a "Adendo A" em qualquer artefato são obsoletas.
3. **A Regra Máxima passou a ter dois níveis de evidência** (núcleo §3.1): unitária por PR, de campo por marco.
4. **Existe uma lista de gates automatizados de CI** (núcleo §11). Regra que não falha o build é sugestão.
5. **ADR ganhou dois níveis** (núcleo §10): ADR completo e Decision Log. Segurança nunca é aprovada por silêncio.
6. **Nenhum dado de produção entra no contexto de agente** (núcleo §5). É a regra nova mais importante daquele conjunto.

---

## O que mudou na versão 2.1 (correções da segunda revisão)

Quatro mudanças alteram o que o agente pode fazer, e são as que importa ler antes de codar:

1. **O Documento 4 deixou de ser fonte de conteúdo arquitetural** (núcleo §0). Módulo não reenuncia os Documentos 2 ou 3: remete. Onde a reenunciação é útil, ela é **gerada** do original, marcada com `<!-- gerado de ... -->` e validada em CI. Motivo: duas listas reenunciadas à mão divergiram da origem, e a hierarquia numerada fazia a cópia vencer a fonte.
2. **Existem itens `[PENDENTE — DECISÃO DO FUNDADOR]`**, além dos `[ABERTO — FASE 0]`. São escolhas de produto com custo de mercado ou de escopo, e o agente não as decide **nem implementa em nenhuma das opções**. São três, listadas no núcleo §0.
3. **Os quatro itens `[ABERTO — FASE 0]` têm localização conferida**, e o CI compara as ocorrências do marcador com a tabela do núcleo §0. Antes, dois dos quatro não tinham marcador no local que o núcleo indicava — e um deles trazia uma "recomendação" que um agente leria como decisão fechada.
4. **O rótulo `aguardando-evidencia-campo` passou a ser aplicado pelo CI**, a partir dos caminhos alterados (núcleo §3.1). O humano só pode removê-lo, com justificativa registrada. Antes, o gate verificava a presença do rótulo, e quem aplicava o rótulo era quem escreveu o código.

Gates novos no núcleo §11: marcadores abertos e pendentes, listas canônicas geradas, idempotência por endpoint, atributos de backup no manifesto, propagação de `modo_teste`, derivação do rótulo de evidência.

---

## O que mudou na versão 2.2 (correções da terceira rodada)

1. **A confirmação de check-in é assinada sem nonce do servidor** (Documento 2, §16.8). A redação anterior exigia nonce em todos os eventos assinados, o que tornava a confirmação impossível offline e inatingível na Fase 1. Nonce agora só nos três eventos que já são online. Decisão em ADR-0013.
2. **`policy_version` existe como seção** (Documento 2, §18.7.2): o que contém, como é versionado, como o aparelho descobre os limites, e o que acontece offline.
3. **A cláusula do núcleo §0 alcança o Documento 5**: o Documento 4 também não é fonte de escopo, fase, critério de aceite ou evidência.
4. **Três gates foram reescritos** porque, como estavam, não podiam entrar em operação: marcadores (agora exige ao menos uma ocorrência na seção citada, e permite remissões em outras), listas canônicas (origens derivadas dos marcadores presentes, não de lista fixa) e `modo_teste` (invariante de ponto único de despacho, em vez de análise de fluxo).
5. **`SEM_CIENCIA` tem saídas** e `COBERTURA_INDISPONIVEL` tem linha na tabela de transições. Os dois estados foram criados na rodada anterior sem serem conciliados com as máquinas que os hospedam.
6. **A Fase 1 tem pré-condições de entrada declaradas**, incluindo a decisão da tela de bloqueio segura — sem ela, a fase tem critério de aceite inatingível em uma classe de aparelho.

## Por ferramenta

**Claude Code:** copie `00-nucleo.md` para o `CLAUDE.md` na raiz do repositório, ou referencie com `@docs/agentes/00-nucleo.md`. Mantenha os módulos em `docs/agentes/` e instrua no `CLAUDE.md`: "antes de codar, leia o módulo indicado na tabela da seção 1 do núcleo e a fase pertinente do Documento 5".

**Cursor:** crie `.cursor/rules/`. O núcleo vira regra com `alwaysApply: true`. Cada módulo vira uma regra com glob: `10-android` com `android/**`, `20-backend` com `backend/**`, `30-seguranca` como `alwaysApply` ou glob amplo, `40` e `50` acionáveis por descrição.

**Windsurf:** mesmo padrão em `.windsurf/rules/`.

**Codex e agentes com `AGENTS.md`:** o núcleo é o `AGENTS.md` da raiz; módulos referenciados por caminho.

**ChatGPT e Gemini (chat):** crie um Project ou Gem com os arquivos anexados e a instrução de sistema: "Siga `00-nucleo.md` integralmente; carregue o módulo indicado pela tabela da seção 1 antes de responder tarefas de código; consulte o Documento 5 para escopo e critério de aceite."

---

## Regras de operação

- Estes arquivos vivem no repositório e mudam por PR mais ADR, como qualquer código.
- Os Documentos 1, 2, 3 e 5 ficam em `docs/` e são consultados por seção, conforme a tabela do núcleo §1.
- Ao detectar regra obsoleta ou conflito entre documentos, o agente **abre issue**. Não ignora e não corrige silenciosamente.
- Ao detectar item marcado `[ABERTO — FASE 0]`, o agente **para e propõe ADR**. Não decide.
- Ao detectar item marcado `[PENDENTE — DECISÃO DO FUNDADOR]`, o agente **para, apresenta as opções com o custo de cada uma, e não implementa nenhuma**.
- Ao encontrar reenunciação divergente de conteúdo dos Documentos 2 ou 3 em módulo do Documento 4: a fonte prevalece, e a divergência vira issue.
- Nenhum agente recebe credencial de produção. Nenhum dado de produção entra em prompt, issue, log de trabalho ou anexo.
