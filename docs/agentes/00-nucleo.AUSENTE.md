# ⚠️ `00-nucleo.md` — AUSENTE

Este arquivo **não é o núcleo**. É um marcador de ausência.

O arquivo real — `docs/agentes/00-nucleo.md`, versão **2.3** — não foi entregue ao
repositório. Nenhum agente deve tratar este marcador como substituto, e nenhum agente deve
escrever um `00-nucleo.md` a partir das remissões: o núcleo é fonte, e reconstruí-lo por
inferência seria inventar exatamente o artefato que existe para impedir invenção.

**Data do registro:** 2026-07-26

---

## Por que a ausência importa

O `README.md` deste diretório determina que o núcleo seja copiado para o `CLAUDE.md` da raiz
e que esteja **sempre** no contexto do agente. Os cinco módulos (10, 20, 30, 40, 50) abrem
declarando "Pressupõe `00-nucleo.md`". Os Documentos 1, 2, 3 e 5 declaram sua posição na
hierarquia citando "Documento 4, núcleo §0". Os relatórios ARB3 e ARB4 verificam o corpus
contra ele.

Sem o núcleo, o projeto opera com as **remissões** ao controle, e não com o controle.

## O que exatamente falta

| Seção | O que contém, segundo quem a cita | Quem depende |
|---|---|---|
| **§0** | Tabela de hierarquia de autoridade; tabela dos **4 itens `[ABERTO — FASE 0]`** com a seção-dona de cada um; tabela dos **3 `[PENDENTE — DECISÃO DO FUNDADOR]`**; cláusula "o Documento 4 não é fonte de conteúdo arquitetural, de estados, de contratos, de modelo de dados, de ameaças, nem de escopo, fase, critério de aceite ou evidência" | Docs 1, 2, 3, 5 (cabeçalhos); mód. 20, 30 (cabeçalhos); mód. 40 §7; mód. 50 §4, §9; ARB3-003, ARB3-016, ARB4-006 |
| **§1** | Tabela módulo × tarefa — qual módulo carregar para cada tipo de trabalho | README; `CLAUDE.md` |
| **§2.2** | "Ativar, registrar, temporizar e sobreviver a reinício **nunca dependem de rede**" — a premissa que sustenta a ausência de nonce no caminho do check-in | Doc 2 §16.8 e §16.8.2; mód. 10 §8; mód. 20 §6; mód. 30 §4; ARB3-001 |
| **§3.1** | Regra Máxima com **dois níveis de evidência**: unitária por PR, de campo por marco; derivação automática do rótulo `aguardando-evidencia-campo` a partir dos caminhos alterados; resíduo humano da remoção do rótulo | mód. 40 §5; mód. 50 §3, §4; ARB2-028, ARB3-020 |
| **§3.2** | Regra sobre declarar critério atendido sem evidência ("a violação mais grave do núcleo §3.2", ARB4-004) | ARB4-004 |
| **§4.6** | Afirmação de plataforma exige fonte oficial | Doc 2 §34.5; mód. 30 §9 |
| **§5** | Nenhum dado de produção no contexto do agente; bug encontrado vira issue mesmo fora do escopo | mód. 50 §10; ARB4-004 |
| **§6** | Idempotência — proibição declarada **absoluta**, motivo pelo qual a tabela do Documento 2 §16.3 não pode ser parcial | Doc 2 §16.3; mód. 20 §2 |
| **§9** | **Template de evidência (canônico)**. Documentos 5 §6 e mód. 40/50 declaram explicitamente não manter template próprio | Doc 5 §6, §7; mód. 40 §5; mód. 50 §1, §3 |
| **§10** | Dois níveis de decisão: ADR completo e Decision Log | mód. 50 §5 |
| **§11** | **Lista canônica dos gates automatizados de CI** | Doc 2 §8, §32; Doc 3 §39.2, §51; Doc 5 §5; mód. 10 §1; mód. 20 §2, §6; mód. 30 §2, §3; mód. 40 §3, §7; mód. 50 §11 |

## O que fica bloqueado enquanto faltar

1. **O template de evidência (§9)** é obrigatório em toda medição da Fase 0 — "cada medição
   usa o template do Documento 4, §9, com executor humano identificado" (Documento 5, Fase 0).
   `docs/fase-0/metodo-de-medicao.md` define o **conteúdo** de cada medição, mas o **formato
   de registro** não pode ser inventado aqui.
2. **Os gates de CI (§11)** não podem ser implementados a partir de cópia: o achado
   **ARB4-005** demonstra que a cópia existente no módulo 40 §7 está desatualizada em três
   gates, e a origem é o §11.
3. **A conferência dos marcadores** (`[ABERTO — FASE 0]`, `[PENDENTE — DECISÃO DO FUNDADOR]`)
   contra a tabela do §0 não é executável: a tabela é a origem.
4. **A hierarquia numerada** dos cabeçalhos (Doc 3 = nível 2, Doc 5 = 4, Doc 2 = 5, Doc 1 = 6)
   não pode ser conciliada com a ordem operacional; o nível 3 não tem dono conhecido e o
   Documento 4 não tem nível declarado em nenhum dos 13 arquivos recebidos.

## Como remover este arquivo

Quando `00-nucleo.md` for adicionado ao repositório: conferir os itens da tabela acima contra
o conteúdo real, resolver o **ARB4-005** e o **ARB4-006** com a origem em mãos, e apagar este
marcador na mesma PR.
