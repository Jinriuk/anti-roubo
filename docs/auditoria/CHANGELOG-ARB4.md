# Changelog — correções da quarta rodada (ARB4)

**Data:** 27/07/2026
**Escopo:** os 6 itens do portão do relatório ARB4, mais 2 de backlog e 4 achados não numerados
em nenhuma rodada.
**Autorização:** decisões D1 a D9 do fundador.
**Versões resultantes:** Documento 2, Documento 3, Documento 5 e módulos 30 e 40 alterados.
Documento 1 e núcleo inalterados.

**Regras respeitadas:** nenhum item `[ABERTO — FASE 0]` fechado; nenhum
`[PENDENTE — DECISÃO DO FUNDADOR]` decidido nem implementado; nenhuma seção renumerada; nenhuma
reescrita fora dos achados apontados; nenhuma afirmação de plataforma nova introduzida.

---

## Itens do portão

| ID | Sev. | Onde | O que mudou |
|---|---|---|---|
| **ARB4-001** | **Crítica** | Doc 2 §16.8.1 (alínea "Ordem"); Doc 5, Fase 2 | Antirreplay e ordenação separados. `sequence` **deixa de ser critério de rejeição**: a confirmação assinada é aceita quando o `check_in_id` está sem uso e o evento está dentro da idade máxima do §16.7; `sequence` não contígua **registra lacuna** (§16.6) e a confirmação vale. Declarado que `highest_sequence` é grandeza por instalação, nunca por `check_in_id`. Critério de aceite correspondente acrescentado à Fase 2. Alínea obrigatória no ADR-0013 |
| **ARB4-002** | Alta | Doc 2 §16.8.2 | O "Efeito" foi reescrito para o que a regra 3 entrega: o desafio de sessão prova que a instalação já falou com o servidor e amarra a assinatura àquela sessão, **sem** limitar a pré-assinatura. O limite continua sendo a configuração da chave (uma autenticação, uma assinatura). Limitar a aceitação de desafio vencido a uma janela de *n* desafios permanece **decisão de desenho do ADR-0013** |
| **ARB4-003** | Alta | Doc 2 §18.7.2 e §18.7 item 2a; mód. 40 §2 | Os dois regimes nomeados em tabela: **sessão registrada** — prazo armado imutável, parâmetro além do máximo rejeitado com `TIMING_PARAM_OUT_OF_POLICY`; **sessão armada offline** — reconciliação na primeira comunicação **pode encurtar**, com aviso, nunca abaixo de prazo já vencido. O teste obrigatório do mód. 40 §2 foi reescrito para os dois casos; antes exigia duas asserções que se excluíam no mesmo cenário |
| **ARB4-004** | Alta | Doc 5, Fase 1 e Fase 2 | Critério da Fase 1 passa a exigir as transições de cobertura **alcançáveis nesta fase** (linhas 6 e 7 do §11.2). As linhas 1 a 5 exigem servidor e viraram critério de aceite da Fase 2. Antes, cumprir o critério exigiria simular servidor — proibido — ou declarar atendido com duas de sete |
| **ARB4-005** | Alta | mód. 40 §7 | Os três gates que o módulo copiava na forma pré-correção — listas canônicas com o §51 dentro de lista fixa, marcadores sem as quatro exigências, `modo_teste` como análise de fluxo — foram substituídos por **remissão pura ao núcleo §11**, no padrão que o módulo 50 §11 já usava |
| **ARB4-010** | Média | Doc 2 §39 | Os três parâmetros órfãos da partição do ADR-0005 entraram no bloco 0005-A: `grace_seconds` provisório, `janela_de_reconciliacao` de 180 s e o teto do termo 9 do §12.5. Acrescentada também a **faixa de intervalo de confirmação**, que o §12.5 termo 1 exigia e nenhum bloco possuía |

## Backlog aplicado na mesma rodada

| ID | Onde | O que mudou |
|---|---|---|
| **ARB4-007** | Doc 5, Fase 0 | Três medições acrescentadas à tabela obrigatória, com critério de aceite: latência do prompt até assinatura verificada (biometria **e** credencial); custo de bateria de N confirmações por sessão; **taxa de confirmação dentro do prazo**, que governa o falso positivo externo — o único que bloqueia release. Acrescentado também o critério que fecha o `VERIFICAR:` do §16.8.3 em aparelho |
| **ARB4-011** | Doc 2 §16.3 | As 9 células vazias da coluna "Método e caminho" preenchidas com os caminhos do §21.2. A linha `Heartbeat`, que tinha 3 células numa tabela de 4 colunas, está corrigida. O gate de idempotência do núcleo §11 passa a poder mapear rota → linha |

## Achados não numerados em nenhuma rodada, corrigidos aqui

Levantados na leitura integral do corpus completo. **São todos resíduos de correções já aceitas
nas rodadas ARB2 e ARB3 que foram aplicadas ao documento inferior e não chegaram à origem** — o
que o próprio corpus chama de bug, não de conflito de hierarquia.

| ID | Sev. | Onde | O que mudou |
|---|---|---|---|
| **E1** | Alta | Doc 5, Fase 0 (Dependências) | A dependência declarava três aparelhos com um no Android atual, e a medição de latência de worker exige **Android 16 e 17, por fabricante** — não era executável em nenhuma das duas versões. Passou a exigir **quatro ou mais** aparelhos, com um em 16 e um em 17 |
| **E2** | Alta | Doc 3 §13.3 | O primeiro controle obrigatório ainda dizia *"confirmação forte: biometria **ou PIN interno**"*, contradizendo o §28.2 do **próprio documento**, o Doc 2 §14.3 e o glossário corrigido pelo ARB3-008. Passou a "biometria **ou o credencial de tela de bloqueio do aparelho**, e a confirmação é assinada por `K_confirmacao`". O changelog ARB2 lista o §13.3 entre os arquivos que o ARB2-001 tocou — a correção estava prevista e não chegou lá |
| **E3** | Média | Doc 3 §8.2 | Ativos digitais listavam "chaves `K_dados` e `K_leitura`, PIN interno" — sem `K_confirmacao`, criada pelo ARB2-004, e com o PIN interno como ativo criptográfico. Corrigido nas duas pontas |
| **E4** | Alta | Doc 3 §27.4 | A tabela dizia que `K_leitura` protege "histórico legível". A correção ARB3-011 já havia mudado isso no Doc 2 §14.3 para "**cache local** do histórico vindo do servidor" — a origem ficou com a redação antiga, e o Documento 3 é hierarquicamente superior |
| **E5** | Média | Doc 3 §34.1; mód. 30 §2 | O módulo 30 acrescentava dois eventos auditáveis **fora** do bloco `<!-- gerado de -->`, isto é, fora do controle que existe para impedir divergência de lista canônica. Os dois — alteração de parâmetro de temporização e prazo suprimido por indisponibilidade — foram incorporados **à origem** (Doc 3 §34.1) e o bloco gerado passou a refleti-los |

## Não corrigido, e por quê

1. **Os quatro `[ABERTO — FASE 0]` seguem abertos.** Onde uma regra precisou de número, entrou
   **valor provisório declarado** com ADR dono, conforme o núcleo §0 — foi o caso da faixa de
   intervalo de confirmação (15/30/60 min, dono ADR-0005-A), declarada porque a medição da
   Fase 0 não pode ser dimensionada sem ela.
2. **As três decisões `[PENDENTE — DECISÃO DO FUNDADOR]` seguem pendentes.** Nenhuma foi
   decidida nem implementada em nenhuma das opções.
3. **ARB4-006** (a cláusula do §0 não alcança o próprio núcleo) **não foi corrigido.** É
   alteração do núcleo §0 e §11 — o documento que governa todos os outros —, e a ARB4 o
   classifica como o item de backlog que subiria acima de todos. Fica como issue, para decisão
   própria.
4. **ARB4-008** (`MasterKeys` do Jetpack impede chave auth-per-use, e nada proíbe o caminho)
   **não foi corrigido.** Confirmei que a proibição não existe nem no mód. 30 §3 nem no mód. 10
   §8 — o achado **não cai**. É proibição nova de API mais um teste novo no mód. 40 §2 ("segunda
   assinatura sem nova autenticação deve falhar"), e ambos alteram regra de código. Fica como
   issue.
5. **ARB4-009** (invalidação de `K_confirmacao` com sessão ativa sem comportamento declarado)
   **não foi corrigido.** Exige escolher entre recriar a chave com step-up ou levar a sessão a
   estado degradado, e definir o que acontece com o prazo já armado no servidor. É decisão de
   desenho, não redação. Fica como issue, com insumo vindo da medição M7.
6. **ARB4-016** (`DELETE /me` retendo chave de idempotência por 30 dias após pedido de exclusão)
   **não foi corrigido.** Confrontei com o Doc 3 §32.3: o conflito é por **omissão** — o §32.3
   não menciona a retenção da chave de idempotência —, não por contradição literal. Resolver
   exige base legal declarada e entrada em Data Safety. Fica como issue.
7. **E6** (Doc 2 §14.4 e Doc 3 §26.5 duplicam a tabela de retenção sem marcador; a hierarquia de
   chaves existe em quatro cópias) **não foi corrigido.** Marcar exige decidir a direção de cada
   par, e para a hierarquia de chaves a direção é genuinamente ambígua: o Doc 3 §27.4 remete ao
   Doc 2 §14.3 para o detalhe, enquanto o Doc 3 é o documento superior. Fica como issue — é o
   ARB4-006 um nível acima.
8. **E7 a E13** (resíduos menores: "confirma com biometria ou PIN" no Doc 1 §15.2; "São duas"
   máquinas no Doc 2 §10; ordem do §39; versão declarada do Doc 2; prazo do ADR-0012;
   categorização da medição de mercado) **não foram corrigidos.** Ficam como issues.
9. **Não revisei o que não foi apontado.** Os achados desta rodada saíram da leitura integral do
   corpus para construção de contexto, não de uma varredura adversarial dedicada.

## Verificação de plataforma

**Nenhuma afirmação de plataforma nova foi introduzida** por estas correções. As afirmações
existentes não foram reverificadas contra fonte oficial nesta rodada — a última verificação
completa é a Verificação 4 do relatório ARB4.

Dois `VERIFICAR:` continuam abertos e são medição da Fase 0. O terceiro — semântica de
`setUserAuthenticationParameters` com tempo zero — a ARB4 declara confirmada em fonte oficial
como **por operação criptográfica**; a confirmação **em aparelho** virou critério de aceite da
Fase 0 nesta rodada, e o marcador permanece no §16.8.3 até ela ocorrer.
