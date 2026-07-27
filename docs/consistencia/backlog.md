# Backlog de consistência documental

**Data do levantamento:** 2026-07-26 · **Atualizado:** 2026-07-27
**Corpus:** os 14 arquivos, incluindo `00-nucleo.md` v2.3.

> ✅ **Onze itens foram corrigidos nos documentos em 2026-07-27**, sob as decisões D1 a D9 do
> fundador: os 6 do portão da ARB4, 2 do backlog dela (ARB4-007 e ARB4-011) e 5 achados não
> numerados em nenhuma rodada (E1 a E5). O registro completo está em
> `docs/auditoria/CHANGELOG-ARB4.md`. Os demais permanecem abertos e viraram issues.

Regra que produz este arquivo: *"Ao detectar regra obsoleta ou conflito entre documentos, o
agente **abre issue**. Não ignora e não corrige silenciosamente"* (Documento 4, README —
Regras de operação; módulo 50, §9 e §10).

Cada linha está redigida para virar issue.

Legenda: ✅ **CORRIGIDO** (documento alterado, ver changelog ARB4) · ✅ **RESOLVIDO** ·
⏳ **PARCIAL** · `ABERTO` (virou issue).

---

## A · Artefatos ausentes

| ID | Estado | Título de issue | Detalhe |
|---|---|---|---|
| A1 | ✅ **RESOLVIDO** | `00-nucleo.md` v2.3 entregue e instalado | O marcador de ausência foi removido. Efeito colateral relevante: a hierarquia do §0 põe o **Documento 4 no nível 3**, acima dos Documentos 5, 2 e 1 — e o `CLAUDE.md` foi corrigido, porque estava com a ordem invertida |
| A2 | ABERTO | Relatório ARB2 (Parte 1) ausente | Citado pela ARB3 como base de conferência das afirmações de plataforma |
| A3 | ABERTO | Instrução de correção do fundador não existe como documento | A ARB3 declara: 9 de 42 itens (MOD-01/02, ADD-01 a 07) verificados contra o que o changelog *declara*, não contra o que foi *pedido* |
| A4 | ⏳ **PARCIAL** | ADR-0001, 0002 e 0003 aceitos; 0004, 0005-A e 0013 seguem inexistentes (pré-condição da Fase 1) | 0001, 0002 e 0003 são dependência de **entrada da Fase 0**; 0004, 0005-A e 0013 são pré-condição da Fase 1. Propostas para os três primeiros em `docs/adr/` |
| A5 | — | `docs/fabricantes/` e `docs/evidencias/` vazios | Esperado: são produto das fases. Diretórios criados |

## B · Portão da ARB4 — bloqueiam o congelamento

Confirmei cada um lendo o texto dos documentos recebidos.

| ID | Sev. | Estado | Título de issue | Local |
|---|---|---|---|---|
| B1 / ARB4-001 | **Crítica** | ✅ **CORRIGIDO** | Regra de ordem do §16.8.1 rejeita confirmação legítima atrasada | Doc 2 §16.8.1. `highest_sequence` é definido em §16.6 **por instalação**; a regra o aplica "para aquele `check_in_id`". Leitura por `check_in_id` = regra vazia; leitura por instalação contradiz §16.4, o **teste obrigatório** do §16.6 (enviar 1,2,4,5; entregar o 3 atrasado) e o mód. 40 §2, que faz de desordem caso obrigatório. Cenário: usuário confirma no metrô, confirma ao sair, outbox entrega fora de ordem, a primeira é rejeitada, a reconciliação depende dela, e **o contato é acionado por quem confirmou duas vezes** |
| B2 / ARB4-002 | Alta | ✅ **CORRIGIDO** | O "Efeito" do §16.8.2 é desfeito pela regra 3 da própria subseção | Doc 2 §16.8.2. O texto afirma que o desafio limita a pré-assinatura a uma confirmação; a regra 3 aceita desafio "conhecido mas não corrente", permitindo assinar *k+1*, *k+2*, *k+3* com o mesmo desafio |
| B3 / ARB4-003 | Alta | ✅ **CORRIGIDO** | Três regras incompatíveis sobre encurtar prazo de sessão em curso | Doc 2 §18.7.2 ("nunca encurta") × §18.7.2 ("reduzido ao limite") × §18.7 item 2a ("rejeitado, prazo anterior permanece"). O mód. 40 §2 exige as duas primeiras como teste obrigatório — a suíte não pode passar |
| B4 / ARB4-004 | Alta | ✅ **CORRIGIDO** | Critério de aceite da Fase 1 inatingível: 5 das 7 transições de cobertura exigem backend | Doc 5, Fase 1 × Doc 2 §11.2. Só as linhas 6 e 7 são alcançáveis na fase; o escopo exclui backend e fixa `COBERTURA_INDISPONIVEL`, declarado mutuamente exclusivo |
| B5 / ARB4-005 | Alta | ✅ **CORRIGIDO** | Módulo 40 §7 carrega três gates na forma pré-correção | mód. 40 §7 × núcleo §11. Confirmei o lado do módulo: listas canônicas com o §51 dentro de lista fixa; marcadores "conferidas contra as tabelas do núcleo §0" sem as quatro exigências; `modo_teste` como análise de fluxo. O mód. 50 §11 remete corretamente — dois módulos do mesmo documento, políticas opostas |
| B6 / ARB4-010 | Média | ✅ **CORRIGIDO** | Partição do ADR-0005 deixou três parâmetros sem bloco dono | Doc 2 §39: `grace_seconds` provisório (§12.5 termo 2), `janela_de_reconciliacao` de 180 s (termo 6) e o teto do termo 9 |

## C · Backlog da ARB4 — confirmados no corpus

| ID | Sev. | Estado | Título de issue | Local |
|---|---|---|---|---|
| C1 / ARB4-011 | Alta | ✅ **CORRIGIDO** | Coluna "Método e caminho" vazia em 9 de 15 linhas da tabela de idempotência | Doc 2 §16.3: Heartbeat, Acionamento manual, Confirmação e encerramento de protocolo, Ciência do contato, Simulação, Convite/concessão/revogação, Registro de token/transferência, Recuperação e step-up, Billing. A linha "Heartbeat" tem **3 células numa tabela de 4 colunas**. O gate de idempotência precisa mapear rota → linha |
| C2 / ARB4-007 | Alta | ✅ **CORRIGIDO** | A Fase 0 não mede o custo do mecanismo que ela existe para validar | Doc 5, Fase 0. Faltam: o `VERIFICAR:` do §16.8.3 (atribuído à Fase 0, sem linha); latência do prompt até assinatura verificada; custo de bateria de N confirmações; **taxa de confirmação dentro do prazo** — que governa o falso positivo externo, o único que bloqueia release. Especificação pronta em `docs/fase-0/metodo-de-medicao.md` §6 |
| C3 / ARB4-009 | Alta | ABERTO | Invalidação de `K_confirmacao` com sessão ativa não tem comportamento declarado | Doc 2 §14.3 × Doc 5, Fase 1. O critério cobre `K_dados` e `K_leitura`; **`K_confirmacao` não aparece** — e é a que impede confirmar um prazo que o servidor já tem armado. Manutenção rotineira de aparelho vira alerta ao contato |
| C4 / ARB4-012 | Média | ABERTO | Cabeçalho de versão desatualizado no Documento 5 | Doc 5, L5: "Alteração da 2.2: correções da segunda rodada (ARB2)" — descreve a rodada anterior, sem citar nenhuma correção ARB3 que produziu a v2.3. A ARB4 aponta o mesmo no núcleo (não verificável) |
| C5 / ARB4-013 | Média | ABERTO | Gate de marcadores promete lista de exceções que não contém | núcleo §11 — **confirmado com o núcleo em mãos**: o gate diz que "texto que *fala sobre* o marcador fica fora da contagem por lista de exceções explícita no próprio gate" e não a enumera |
| C6 / ARB4-014 | Baixa | ABERTO | Mecanismo da notificação em tela cheia descrito incorretamente | Doc 2 §12.4. Efeito certo; pela documentação de plataforma a permissão é concedida por padrão em Android 14 e **a Play Store a revoga na instalação**. Consequência de teste: o ensaio precisa rodar em build **instalado pela Play** (faixa interna), nunca por APK lateral |
| C7 / ARB4-015 | Baixa | ABERTO | Checklist de publicação sem linha sobre permissão de contatos | mód. 50 §8. A política está decidida no §34.5 ("evitada: entrada manual ou seletor do sistema"); falta a linha que impede o claim de regredir em silêncio |
| C8 / ARB4-006 | Alta | ABERTO | A cláusula do §0 não alcança o próprio núcleo | Nada cobre reenunciação **do núcleo pelos módulos**, e o gate de listas geradas só conhece origens nos Documentos 2 e 3. **B5 é a instância.** A ARB4 recomenda subir este acima de todos os outros de backlog |

## D · Itens que a ARB4 não pôde emitir e que o corpus agora resolve

A ARB4 trabalhou com **9 de 14 arquivos**. Registro, não veredito — não sou o Verification Board.

| ID | Estado | Constatação |
|---|---|---|
| D1 | **RESOLVIDO PELO CORPUS** | ARB4 **item 1** (NÃO EMITÍVEL) — coerência do §16.8 nos módulos. **Confere nos três:** mód. 10 §8 traz a tupla sem nonce e "não pedir nonce ao servidor para confirmar"; mód. 20 §6 traz os dois regimes e "nunca exigir nonce aqui"; mód. 30 §4 traz "material do próprio aparelho, sem nonce" |
| D2 | **RESOLVIDO PELO CORPUS** | ARB4 **item 7** (NÃO EMITÍVEL) — o bloco `<!-- gerado de Documento 3, §20.3 -->` **existe** no mód. 20 §6 |
| D3 | **ABERTO — o achado NÃO cai** | ARB4-008 (`MasterKeys`). Li mód. 30 §3 e mód. 10 §8 integralmente: **nenhum dos dois proíbe `MasterKeys`** nem wrapper que imponha duração > 0. Um agente que siga o caminho "recomendado" do Jetpack cria chave baseada em tempo e a garantia central desaparece **sem nenhum teste falhar** — a asserção "uma autenticação, uma assinatura" **passa** numa chave de janela curta se o teste assinar uma vez |
| D4 | ABERTO — por omissão | ARB4-016 (`DELETE /me`). Doc 3 §32.3 trata exclusão lógica, fila de purga, propagação e expiração de backups — **não menciona** a retenção da chave de idempotência. A retenção de 30 dias de `(chave, usuário, hash, resposta)` **após** o pedido de exclusão segue sem base legal declarada nem entrada em Data Safety |

## E · Achados que não encontrei numerados em ARB3 nem em ARB4

| ID | Sev. proposta | Título de issue | Detalhe |
|---|---|---|---|
| **E1** ✅ CORRIGIDO | **Alta** | A Fase 0 exige medição em Android 16 sem declarar aparelho em Android 16 | Doc 5, Fase 0. A M9 pede "latência de worker com e sem FGS, **em Android 16 e 17**, p50/p95, **por fabricante**". As dependências declaram três aparelhos: dois abaixo de API 33 e **um** no Android 17. Não há aparelho em 16, e com um só em 17 "por fabricante" também não é executável. Afeta a **compra de hardware** |
| **E2** ✅ CORRIGIDO | **Alta** | Documento 3 §13.3 ainda declara PIN interno como fator de confirmação forte | *"Confirmação de check-in exige confirmação forte: biometria **ou PIN interno**"*. Contradiz o §28.2 do próprio Documento 3, o §14.3 do Documento 2, o glossário corrigido pelo ARB3-008 e o mód. 30 §4. **O Documento 3 é hierarquicamente superior ao Documento 2** — a correção foi aplicada no documento inferior e não na origem |
| **E3** ✅ CORRIGIDO | Média | Documento 3 §8.2 lista PIN interno como ativo criptográfico e omite `K_confirmacao` | *"chaves `K_dados` e `K_leitura`, PIN interno"*. A terceira chave, criada pela correção ARB2-004, não consta dos ativos protegidos |
| **E4** ✅ CORRIGIDO | **Alta** | Documento 3 §27.4 não recebeu a correção ARB3-011 aplicada ao Documento 2 §14.3 | A tabela diz que `K_leitura` protege "**histórico legível**"; o Doc 2 §14.3 foi corrigido para "**cache local** do histórico vindo do servidor". A origem superior ficou com a redação antiga |
| **E5** ✅ CORRIGIDO | Média | Módulo 30 §2 estende a lista canônica de eventos auditáveis **fora** do bloco validado em CI | Logo abaixo do bloco `<!-- gerado de Documento 3, §34.1 -->` acrescenta "alteração de parâmetro de temporização da sessão" e "prazo suprimido por indisponibilidade própria". **Nenhum dos dois consta do §34.1 de origem.** Divergência por adição, fora do controle que existe para impedi-la |
| **E6** | **Alta** | Documento reenunciando documento não é coberto por nenhuma cláusula | A tabela de retenção local está duplicada literalmente em Doc 2 §14.4 e Doc 3 §26.5, **sem marcador `<!-- gerado de -->` em nenhuma**. A hierarquia de chaves existe em **quatro cópias** (Doc 2 §14.3, Doc 3 §27.4, mód. 10 §8, mód. 30 §3) — e a do Doc 3 já divergiu (E4). A cláusula do núcleo §0 cobre o Doc 4 reenunciando os Docs 2/3/5; **não cobre Doc 3 ↔ Doc 2**. É o ARB4-006 um nível acima |
| **E7** | Baixa | Documento 1 §15.2 mantém "confirma com biometria ou PIN" | Resíduo do PIN interno na jornada de uso diário, enquanto §15.1 e §21.1 do mesmo documento já tratam o papel do PIN como pendente |
| **E8** | Baixa | Documento 2 §10 diz "São duas" máquinas; o corpus exige três | §10 abre com *"São **duas**"* e *"única fonte de estados"*, enquanto §11.2 hospeda a terceira e o mód. 40 §2 e §3 exigem "as três máquinas". Registrado pela ARB4 sem número |
| **E9** | Baixa | Documento 2 §39 lista ADR-0013 antes do ADR-0012 | Ordem sequencial quebrada na própria tabela. Registrado pela ARB4 sem número |
| **E10** | Baixa | Documento 2 declara versão 2.1 no cabeçalho e 2.1.1 no corpo | O §16.8 documenta "Correção da v2.1 → v2.1.1"; os artefatos de auditoria e o nome do arquivo tratam como 2.1.1 |
| **E11** | Baixa | Prazo do ADR-0012 conflita entre o §39 e o critério de aceite da Fase 0 | §39 diz "**Após** a Fase 0"; o critério de aceite da Fase 0 exige que seja **proposto** dentro dela |
| **E12** | Baixa | Medição de mercado numa tabela declarada "por fabricante e por estado de bateria" | Doc 5, Fase 0: "parcela do público-alvo sem tela de bloqueio segura" é pesquisa de mercado, sem executor nem método declarados como as demais linhas |
| **E13** | Média | O segundo critério do ADR-0002 não tem fonte prevista em nenhuma fase | Doc 2 §39 exige `minSdk` decidido "por dados de mercado **e** por custo da faixa". A distribuição de versões do Android no público-alvo brasileiro não está na tabela de medições da Fase 0 — nem em nenhuma outra. Proposta: M15, na mesma coleta da M11 |

## F · Nomenclatura — conferido, sem achado

Varredura pelos termos abolidos no glossário (Doc 2, §3): "Dead Man Switch", "Modo Suspeito",
"nível suspeito", "modo protegido", "destruição progressiva", "painel remoto" — **nenhuma
ocorrência** nos 13 arquivos. "Adendo A" aparece apenas onde é declarado obsoleto.
`GET /events/sync` aparece apenas onde é declarado removido.

## G · Hierarquia

| ID | Estado | Título de issue |
|---|---|---|
| G1 | ABERTO | Artefatos de auditoria e changelog sem posição declarada na hierarquia — tratados neste repositório como **não normativos**, subordinados aos documentos que auditam |
| G2 | ABERTO | O README do Documento 4 declara-se "humano; não vai ao contexto do agente", mas é o único artefato que carrega o changelog das versões do próprio Documento 4 — informação de que o agente precisa |
| G3 | ABERTO | O Documento 4 não tem nível numerado declarado em nenhum dos 13 arquivos; o nível 3 da escala não tem dono conhecido. Não resolvível sem o núcleo §0 |

---

## Resumo

| Grupo | Itens |
|---|---|
| A — artefatos ausentes | 5 (4 acionáveis) |
| B — portão da ARB4 | 6 · **1 Crítica, 4 Altas, 1 Média** |
| C — backlog da ARB4 confirmado | 8 |
| D — resolvidos ou mantidos com o corpus novo | 4 · **2 resolvidos, 2 seguem abertos** |
| E — não numerados em nenhuma rodada | 13 · **4 Altas, 3 Médias, 6 Baixas** |
| F — nomenclatura | 0 achados |
| G — hierarquia | 3 |
| **Total acionável** | **34**, dos quais **11 corrigidos em 2026-07-27** e **23 abertos como issue** |

## O que segue aberto e por quê

Os 23 restantes não são resíduo de esquecimento — cada um exige **decisão de desenho, alteração
do núcleo ou base legal**, e nenhum é redação. Os quatro que eu subiria primeiro:

1. **ARB4-006** — estender a cláusula do §0 ao próprio núcleo, e acrescentar `00-nucleo.md §11`
   como origem do gate de listas geradas. A ARB4 o classifica como o item que subiria acima de
   todos, e o **ARB4-005 foi a instância**: o núcleo mudou, o módulo não, e nada detectou.
2. **ARB4-008** — proibir `MasterKeys` e qualquer wrapper que imponha duração > 0 para
   `K_leitura` e `K_confirmacao`, e acrescentar ao módulo 40 §2 o teste que fecha o buraco:
   **segunda assinatura sem nova autenticação deve falhar**. É o modo de falha mais silencioso
   da suíte — a asserção "uma autenticação, uma assinatura" **passa** numa chave de janela curta
   se o teste assinar uma única vez.
3. **ARB4-009** — comportamento de `K_confirmacao` invalidada com sessão ativa. Cadastrar uma
   digital nova numa terça-feira aciona o contato. É manutenção rotineira de aparelho, não
   cenário adversarial.
4. **E6** — as cópias sem marcador entre Documento 2 e Documento 3. É o ARB4-006 um nível acima:
   documento reenunciando documento não é coberto por cláusula nenhuma, e a cópia do Doc 3 §27.4
   **já havia divergido** (E4).

**Nenhum deles bloqueia a Fase 0.** Todos alcançam a Fase 1 ou posterior.
