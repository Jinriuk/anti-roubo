# Verification Board — quarta rodada (ARB4)
## Modo Rua — verificação de congelamento

**Data:** 26/07/2026 · **Material:** 9 de 14 arquivos (Doc 2 v2.1.1, Doc 5 v2.3, núcleo v2.3, mód. 40, mód. 50, README, ARB3, changelog ARB2, changelog ARB3)
**Achados:** 17 · **No portão:** 6 · **Decisão A:** ❌ NÃO CONGELAR

---

## 0. Discordância, primeiro

**O portão desta rodada continua manipulável, e a alavanca agora é a coluna "conteúdo esperado" — escrita por quem escreveu as correções.** A rodada acertou ao matar a reclassificação Verificação/Regressão/Novo. Mas o item 4 é o caso limpo do problema novo: o conteúdo esperado é *"Documento 5, Fase 1, escopo dizendo `COBERTURA_INDISPONIVEL`"*. Uma string, em um lugar. Confere — e é justamente o casamento dessa correção com a do item 6 que produziu o **ARB4-004** desta rodada, um critério de aceite da Fase 1 que ficou inatingível. O portão aprovou a string e não olhou o efeito. Verificação de presença textual é revisão de diff, e a opção do meio da Decisão A já faz isso mais barato.

Segundo ponto: a ordem de leitura anda contra a independência. "Leia primeiro: relatório ARB3, changelog." O changelog é a alegação de quem corrigiu; lê-se por último, não em segundo.

Terceiro, e é o que sustenta congelar mais do que qualquer item desta lista: 118 achados em três rodadas contra **zero linha de código**, com a curva 64 → 33 → 21. Os achados que eu produzi abaixo confirmam a tendência — nenhum é redesenho, cinco dos seis do portão são uma frase cada. O defeito seguinte não está no texto.

**Cobertura.** Não recebi Documento 1, Documento 3, mód. 10, mód. 20 e mód. 30. Isso deixa **dois dos nove itens não emitíveis** (1 e 7, cuja parte final vive nos módulos 10, 20 e 30) e uma pergunta da Verificação 3 aberta (`DELETE /me` contra Doc 3 §32.3, ver ARB4-016). Registro como limite, não como veredito: nenhum dos dois é NÃO CONFERE, porque não encontrei defeito neles — encontrei ausência de material. E o **ARB4-001** dá motivo concreto para desconfiar exatamente dessas três cópias.

---

## VERIFICAÇÃO 1 — os nove

### Item 1 · ARB3-001 (nonce) — **NÃO EMITÍVEL**

Tudo o que é verificável **confere**. §16.8 tem 16.8.1, 16.8.2 e 16.8.3. A tupla não contém nonce:

> `assinatura = Sign_K_confirmacao(installation_id, session_id, check_in_id, sequence, expected_next_checkin_at, boot_id, desafio_de_sessao?)`

Nonce restrito aos três eventos online: *"Encerramento de sessão, encerramento de protocolo e alteração de parâmetro de temporização — nonce do servidor. Esses três já são online por natureza."* Replay atribuído corretamente: *"`event_dedup(installation_id, event_id)` já rejeita reenvio, e a linha de idempotência por `check_in_id` garante que uma assinatura capturada só satisfaz o check-in que ela nomeia, e só uma vez."* Desafio opcional com regra de ausência em §16.8.2, item 4. Critério da Fase 1 coerente: *"assinada por `K_confirmacao` sobre material do próprio aparelho (Documento 2, §16.8.1), **sem desafio do servidor**"*.

Falta a última cláusula do conteúdo esperado — coerência em mód. 10 §8, mód. 20 §6 e mód. 30 §4 — e esses três arquivos não chegaram. Não emito CONFERE sobre parte que não li, e não emito NÃO CONFERE sem defeito. **Pendente de três arquivos**, e é a única coisa que falta para fechar o portão inteiro.

### Item 2 · ARB3-002 (idempotência) — **CONFERE**

§16.3 tem as três linhas e a coluna de método e caminho:

> `| **Alteração de parâmetro de temporização** | PATCH /street-mode/sessions/{id}/timing | Idempotency-Key do cliente; a resposta devolve o policy_version vigente | 24 h |`
> `| **Atualização de instalação** | PATCH /installations/{id} | ... | 24 h |`
> `| **Exclusão de conta** | DELETE /me | ... repetição devolve o estado do pedido já registrado | 30 dias |`

E §21.2 traz `PATCH /street-mode/sessions/{id}/timing`. Confere. Mas ver **ARB4-011**: a coluna que sustenta o gate está vazia em 9 das 15 linhas.

### Item 3 · ARB3-003 (gate de marcadores) — **CONFERE**

Núcleo §11 tem as quatro exigências: *"Cada item das tabelas do §0 tem **ao menos uma** ocorrência do marcador na seção citada; falta de ocorrência falha o build. Ocorrências em outras seções são **permitidas** e devem citar o item dono. A cadeia literal conferida é `[ABERTO — FASE 0]` e `[PENDENTE — DECISÃO DO FUNDADOR]`, com travessão, não hífen. Texto que fala sobre o marcador fica fora da contagem por lista de exceções."* Núcleo §0: `margem_de_rede` → **0005-B (dono único)**.

**Testei o gate corrigido contra o corpus recebido, e ele passa.** Os quatro `[ABERTO — FASE 0]` têm ocorrência na seção citada (Doc 2 §12.2 L386, §34.5 L1163/1173, §18.7 L755; Doc 5 Fase 0 L115) e os três `[PENDENTE]` também (Doc 2 §14.3 L509, §10.2 L277, §10.4 L288). Era o item que a ARB3 dizia não poder entrar em operação; agora entra. Ressalva menor em **ARB4-013**.

### Item 4 · ARB3-004 (Fase 1, cobertura) — **CONFERE**

Escopo da Fase 1: *"Estado de cobertura visível, sempre `COBERTURA_INDISPONIVEL` nesta fase (Documento 2, §11.1 e §11.2, linha 7). **Não** `COBERTURA_LOCAL`: o texto obrigatório daquele estado promete que a conexão resolveria, e nesta fase não existe servidor a alcançar."* Está no escopo, não só no critério. Confere — e produziu o ARB4-004.

### Item 5 · ARB3-005 (`SEM_CIENCIA`) — **CONFERE**

Lista de abertura do §10.2 inclui `SEM_CIENCIA`. É origem nas linhas 9, 10 e 12, mais a 15 (`ciencia_do_contato` → `ALERTANDO`). A linha coringa exclui corretamente: *"qualquer estado exceto SEM_CIENCIA | `ciencia_do_contato` | sem mudança"*. Sem errata no corpo — a nota que resta é histórica ("na primeira redação desta correção nenhuma linha saía dele"), não instrução ao leitor. As "quatro de `SEM_CIENCIA`" do mód. 40 §3 casam com 9, 10, 12 e 15.

### Item 6 · ARB3-006 (`COBERTURA_INDISPONIVEL`) — **CONFERE**

§11.2 linha 7: *"`ativacao_em_build_sem_backend` | COBERTURA_INDISPONIVEL | **constante de build**: entra por configuração, não tem transição de saída e é mutuamente exclusivo dos demais estados. Exclusivo da Fase 1"*. E na matriz Sessão × Cobertura: *"qualquer | INDISPONIVEL | texto próprio de build sem backend; nenhum outro estado de cobertura é alcançável nesse build"*. Confere — e é a outra metade do ARB4-004.

### Item 7 · ARB3-007 (listas geradas) — **NÃO EMITÍVEL**

O que é verificável confere. Núcleo §11: *"A lista de origens é derivada dos marcadores presentes no corpus, **não** de uma lista fixa no gate — hoje: Doc 2 §12.3, Doc 2 §18, Doc 3 §34.1, Doc 3 §20.3. Lista canônica sem cópia (Doc 3, §51, apenas remetida) não entra no gate, e **nenhum texto pode afirmar validação em CI sem carregar o marcador**."* §51 tratado como remissão ✓. Varri Doc 2, Doc 5, núcleo, mód. 40 e mód. 50: nenhuma afirmação de "validada em CI" fora do núcleo §0, que remete ao gate ✓.

O bloco `<!-- gerado de Documento 3, §20.3 -->` deve existir no mód. 20 §6, que não chegou. Mesma situação do item 1.

### Item 8 · ARB3-008 (glossário) — **CONFERE**

> `| Confirmação forte | strong_confirmation | Confirmação autenticada por biometria **ou pelo credencial de tela de bloqueio do aparelho**, e assinada por K_confirmacao (§16.8). Um PIN interno do aplicativo não satisfaz a exigência de autenticação de chave do Keystore (§14.3) |`

"PIN interno" saiu como fator. As três entradas novas — `K_confirmacao`, `SEM_CIENCIA`, `COBERTURA_INDISPONIVEL` — estão lá.

### Item 9 · ARB3-009 (entrada da Fase 1) — **CONFERE**

Seção "Pré-condições de entrada" com ADR-0004, **ADR-0005-A**, **ADR-0013** e a decisão da tela de bloqueio, com o motivo técnico escrito (*"`K_confirmacao` exige `setUserAuthenticationRequired(true)` e não é criável em aparelho sem tela de bloqueio"*), mais a alternativa: *"escopar esta fase apenas para aparelhos com tela de bloqueio segura, declarando isso como limitação **da fase**, não do produto"*.

### Backlog da terceira rodada

**Aplicado: 10 de 11.** Falta **ARB3-018** — a coluna existe, e 9 das 15 linhas não a preenchem (ARB4-011). Os outros dez conferi um a um: 010 (lista sem errata), 011 (`K_leitura` = cache), 012 (termo 9), 013 (default declarado interino), 014 (política renomeada), 015 (ADR-0011 provisório na Fase 0 / definitivo antes da Fase 3), 016 (cláusula do §0 alcança o Doc 5: *"nem de escopo, fase, critério de aceite ou evidência"*), 017 (`modo_teste` virou invariante de ponto único de despacho), 019 (§10.4 movido para depois de §10.3), 020 (resíduo declarado no §3.1).

---

## VERIFICAÇÃO 2 — ataque ao §16.8

**Veredito: sólido com ajustes.** Remover o nonce foi correto e concordo com os cinco descartes da tabela do §16.8 — em particular, o lote de nonces é inútil contra atacante local pelo motivo que a própria seção dá, e a derivação encadeada é o lote com outro nome. **Dois dos dez pontos não fecham, e um deles é Crítico.**

**1 · Pré-assinatura — não fecha, e o motivo não é o que o texto diz.** A defesa está escrita: *"uma autenticação produz exatamente uma assinatura"*, com proibição de cache, lote e janela de validade. A semântica está confirmada (Verificação 4). Mas a garantia liga assinatura a **autenticação**, não a **prazo**, e todo campo da tupla é previsível pelo aparelho. A frase que sustenta o risco residual é esta, e está errada:

> *"Pré-assinar N confirmações exige N autenticações coagidas, e quem tem esse acesso confirmaria ao vivo de todo modo."*

Não confirmaria. Confirmar ao vivo exige manter a vítima disponível em cada prazo futuro; pré-assinar N confirmações permite liberar a vítima e ainda suprimir o vigilante por N intervalos. Para um produto de roubo e coação, são desfechos materialmente diferentes. **O que fecha a maior parte disso é servidor, e é barato:** rejeitar confirmação cujo `expected_next_checkin_at` esteja mais de um intervalo à frente do último prazo confirmado, e tratar lote de confirmações futuras chegando junto como sinal da guarda de anomalia (§18.7.1). Entra em §16.8.3, custa nada offline, e converte "N intervalos suprimidos" em "um".

**2 · Quem decide "nunca registrou sessão" — fecha.** §16.8.2 item 4 amarra a exceção a um fato que só o servidor tem (*"quando a instalação nunca registrou sessão no servidor"*). Resíduo pequeno: falta a linha que rejeita confirmação para sessão conhecida vinda de instalação não associada a ela, que é o caminho reinstalação. Uma frase.

**3 · `sequence` sob controle do aparelho — NÃO FECHA. Crítico.** Ver **ARB4-001**. É o achado principal da rodada.

**4 · Replay entre sessões e instalações — fecha.** `session_id` + `installation_id` na tupla, e a Fase 1 já exige provar identidade e contador novos nos dois caminhos de backup (nuvem e D2D).

**5 · `boot_id` — inerte, como está.** Não previne replay (`check_in_id` previne) e não prova frescor (é estável no boot). O uso legítimo existe — sinalizar que a confirmação nasceu em boot diferente do que registrou o prazo, insumo da janela de reconciliação — e nenhuma das três subseções o consome. Campo inerte em tupla assinada é dívida de compatibilidade, porque o formato congela todos.

**6 · Confirmação offline atrasada — não fecha por omissão.** §16.8 não menciona a janela de reconciliação. §10.2 linha 4 dá `SUSPEITA → FALSO_POSITIVO` por `confirmacao_tardia_do_titular`, e §18.7 item 4 dá os 180 s. Faltam a segunda e a terceira janelas: fora da reconciliação mas dentro da idade máxima (§16.7) — não retrata, registra, e **conta no numerador de falso positivo**, senão a métrica que libera release nasce enviesada; e além da idade máxima — descarta com `EVENT_TOO_OLD` e **audita**, senão o produto não distingue "não confirmou" de "confirmou e nós perdemos".

**7 · A verificação da Fase 1 é honesta, e falta uma asserção.** O critério diz o que faz e por quê: *"com a assinatura verificada nos testes pela chave pública local — nesta fase não existe servidor, e o critério é verificável assim"*. Correto. Falta a asserção que carrega para a Fase 2: **codificação canônica da tupla**, especificada e testada. Sete campos, um opcional, e nenhuma regra de ordem, separador ou encoding. É a origem clássica de divergência entre assinador e verificador, e o servidor da Fase 2 tem de reconstruir os mesmos bytes.

**8 · Chave não criável — pendência bem posta, caminho de erro ausente.** §14.3 põe a decisão como `[PENDENTE]` com duas opções e custos, e a Fase 1 a promove a pré-condição de entrada. Correto. Mas o tratamento da falha de criação da chave não depende da decisão e não está escrito: qualquer que seja o ramo, o app não pode entrar em estado de sessão que prometa garantia que não produz.

**9 · Rotação e revinculação — não fecha.** Ver **ARB4-010**.

**10 · Custo de bateria e latência — não medido.** Ver **ARB4-003**.

---

## VERIFICAÇÃO 4 — plataforma

**1 · Notificação em tela cheia — CONFIRMADA.** A Play Console Help diz que, a partir de 22/01/2025, para apps que alvejam Android 14+, só os de chamada ou despertador têm a permissão habilitada por padrão, e os demais precisam obtê-la do usuário, degradando com elegância se negada. Data, escopo, critério e fonte no §12.4 conferem. Ressalva de mecanismo em **ARB4-014**.

**2 · Escopo mínimo de `ACCESS_FINE_LOCATION` — CONFIRMADA, e a data é declaradamente prevista.** A fonte oficial diz que a fiscalização para apps que alvejam Android 17+ está *prevista* para o fim de outubro de 2026, que o prazo "vai endurecer" com o retorno dos desenvolvedores, e que todos os apps que alvejem 17+ precisam cumprir. O §34.5 diz exatamente isso, com "prevê início de fiscalização" — redação correta. E a correção da justificativa anterior estava certa: a política de escopo mínimo tem vigência própria em 28/10/2026; o que salva o produto é o alvo 36, não a política ser outra. Vale registrar as duas datas separadamente (vigência e fiscalização por alvo), porque são duas.

**3 · `setUserAuthenticationParameters` com tempo zero — POR OPERAÇÃO. O `VERIFICAR:` do §16.8.3 pode fechar.**

A referência de plataforma divide as operações em auth-per-use e baseada em tempo pelo parâmetro `timeout`, e o `CryptoObject` serve para desbloquear chaves auth-per-use, enquanto chaves baseadas em tempo ficam liberadas pela duração especificada. A publicação Android Developers é explícita: um `CryptoObject` é específico de uma operação criptográfica em particular, e a autenticação à qual ele é passado desbloqueia **apenas aquela operação** — é assim que se garante que cada uso da chave seja autenticado.

Portanto a premissa do §16.8.3 se sustenta, e a Verificação 2 item 1 **não** muda de resposta por esta via. Muda por outra, que é o argumento do "confirmaria ao vivo de todo modo".

**Armadilha que precisa entrar no texto:** ver **ARB4-008**.

**Varredura por afirmação nova:** nenhuma afirmação de plataforma nova incorreta em Doc 2, Doc 5, núcleo, mód. 40 e mód. 50. O `[ABERTO — FASE 0]` do §12.2 sobre tipos de FGS, cotas de job do Android 16 e proibições de `BOOT_COMPLETED` reproduz o que a ARB2 já sustentou. A política de contatos, que eu havia apontado como ausente sobre material incompleto, **está tratada** no §34.5 (*"Contatos | política de contatos | evitada: entrada manual ou seletor do sistema | decidido"*) — a correção do meu próprio achado está em **ARB4-015**, rebaixado.

---

## ACHADOS

### ARB4-001 · `sequence` monotônico rejeita confirmação legítima atrasada

**Origem:** Verificação 2 (item 3) e Verificação 3 · **Severidade: Crítica** · **Doc 2 §16.8.1**

**Problema.** A regra de ordem, criada por esta correção:

> *"`sequence` é monotônico por instalação e nunca reinicia (§16.2). O servidor rejeita assinatura cuja `sequence` não avance em relação ao `highest_sequence` conhecido **para aquele `check_in_id`**."*

`highest_sequence` é definido no §16.6 **por instalação**, não por `check_in_id`. As duas leituras possíveis são ambas defeituosas:

- **por `check_in_id`:** a regra é vazia. A idempotência do §16.3 já admite uma única confirmação por `check_in_id`; não há segundo valor com que comparar;
- **por instalação:** a regra rejeita confirmação legítima que chegue fora de ordem — e isso contradiz três lugares. §16.4 (*"o servidor nunca decide por `occurred_at`... usa `sequence`, horário do servidor e regras de reconciliação"*), §16.6, cujo **teste obrigatório** é *"enviar 1, 2, 4, 5 e provar a detecção; entregar o 3 atrasado e provar a reconciliação"*, e o mód. 40 §2, que faz de desordem e duplicata **casos obrigatórios** para todo código de sync e ingestão.

**Consequência.** Na leitura por instalação, este é o cenário: o usuário confirma o check-in *k* no metrô (`COBERTURA_SUSPENSA`), a fila não drena, confirma *k+1* ao sair, e o outbox entrega *k+1* primeiro. A confirmação *k* chega, não avança `sequence`, é **rejeitada**. Se o prazo de *k* venceu e o vigilante abriu `SUSPEITA`, a reconciliação da janela de 180 s depende exatamente dessa confirmação — que o servidor acabou de recusar. O protocolo segue para `ALERTANDO` e **o contato é acionado por causa de um usuário que confirmou corretamente duas vezes.** É a consequência do ARB3-001 reintroduzida por outro mecanismo: a correção trocou o nonce, que impedia a confirmação de existir, por uma regra de ordem que impede a confirmação de ser aceita. Aparece na Fase 4, e o teste do §16.6 falha antes, na Fase 2.

**Recomendação.** Separar antirreplay de ordenação, que é o que o resto do documento já faz:
- antirreplay: `event_dedup(installation_id, event_id)` mais a linha de idempotência por `check_in_id` — já especificados no próprio §16.8.1, e suficientes;
- `sequence`: **detecção de lacuna** (§16.6), nunca critério de rejeição. Aceitar fora de ordem dentro da idade máxima do §16.7;
- reescrever a alínea "Ordem" para dizer que a assinatura é aceita se o `check_in_id` está sem uso e a `sequence` está dentro da janela de idade máxima, com lacuna registrada quando não for contígua.

Custo: um parágrafo, e uma alínea no ADR-0013, que ainda não existe. Não é redesenho.

**Justificativa.** Rejeição por monotonicidade é controle de transporte ordenado. Este transporte é declaradamente offline-first e fora de ordem, por decisão do §16.1 e do §17, e o §16.6 tem teste obrigatório do caso. O mecanismo importou uma premissa que a arquitetura não tem.

**Entra no portão?** **Sim, pelas duas vias:** regressão introduzida pela reescrita do §16.8, e achado Crítico na Verificação 2.

---

### ARB4-002 · O "Efeito" do §16.8.2 é desfeito pela regra 3 da própria subseção

**Origem:** Verificação 2 (item 1) · **Severidade: Alta** · **Doc 2 §16.8.2**

**Problema.** A subseção afirma:

> *"Efeito: com rede em algum momento, o atacante consegue pré-assinar no máximo a confirmação seguinte, não uma sequência delas."*

Isso exige que desafio vencido seja rejeitado. A regra 3, três linhas acima, faz o contrário: *"O servidor aceita desafio **conhecido mas não corrente** enquanto `sequence` avança e o `check_in_id` está sem uso."* Com um desafio conhecido e vencido em mão, o atacante assina *k+1*, *k+2*, *k+3* — todos com o mesmo desafio, todos aceitos, porque cada um nomeia um `check_in_id` sem uso.

**Consequência.** O ganho declarado do §16.8.2 não existe no formato em que ele foi escrito. O leitor — agente ou fundador — conclui que o desafio limita a pré-assinatura a um intervalo, e ela continua ilimitada. É afirmação de controle mais forte que o controle, numa seção que existe para corrigir exatamente esse defeito ("trocar um exagero por outro mais sutil não seria correção", §16.8).

**Recomendação.** A tensão é real e não se resolve por redação: aceitar desafio vencido é necessário para a garantia local, e é o que destrói o efeito. Escolher um:
- manter a regra 3 e **reescrever o "Efeito"** para o que ela entrega — o desafio prova que a instalação já falou com o servidor alguma vez e amarra a assinatura àquela sessão, sem limitar a pré-assinatura;
- ou limitar a aceitação de desafio vencido a uma janela (por exemplo, *n* desafios atrás, ou o desafio da última comunicação bem-sucedida), aceitando que confirmação muito antiga passa a exigir o piso do §16.8.1.

A primeira é uma frase. A segunda é decisão, e cabe no ADR-0013.

**Entra no portão?** **Sim** — regressão introduzida pela reescrita do §16.8.

---

### ARB4-003 · Três regras conflitantes sobre reduzir prazo de sessão em curso

**Origem:** Verificação 3 · **Severidade: Alta** · **Doc 2 §18.7.2 e §18.7 item 2a · mód. 40 §2**

**Problema.** A seção nova diz duas coisas incompatíveis, e o item 2a diz uma terceira. Para uma sessão armada offline cujo intervalo excede o limite vigente:

| Onde | Regra |
|---|---|
| §18.7.2, "Versionamento" | *"Alteração de limite **nunca** encurta o prazo de uma sessão em curso"* |
| §18.7.2, "Como o aparelho descobre" | *"valor local acima do limite vigente é reduzido ao limite, com evento registrado e aviso ao usuário"* |
| §18.7 item 2a | *"valor além do máximo é rejeitado com código estável e **o prazo anterior permanece armado**"* |

A defesa possível é que "sessões já registradas sob elas" exclui a sessão armada offline, que nunca foi registrada. O texto não diz isso, e o mód. 40 §2 exige as **duas** asserções contraditórias como teste obrigatório: *"valor local acima do limite vigente é reduzido ao limite na primeira comunicação, com evento e aviso; alteração de limite não encurta prazo de sessão em curso."*

**Consequência.** O usuário ativa em `COBERTURA_LOCAL` com intervalo de 8 h sob o `policy_version` em cache; o servidor vigente limita a 4 h; na primeira comunicação o aparelho reduz e passa a pedir confirmação 4 h antes do que o usuário configurou. Aviso mitiga, o prazo mudou. E a suíte de testes especificada não pode passar: as duas asserções se excluem nesse cenário. Aparece na Fase 2.

**Recomendação.** Uma frase que nomeie o recorte: a proteção da "Versionamento" vale para sessão **registrada** no servidor; sessão armada offline reconcilia na primeira comunicação, e a reconciliação **pode** encurtar — com aviso, e nunca abaixo do prazo já vencido. E alinhar o item 2a: rejeitar-e-manter aplica-se a parâmetro submetido em sessão registrada; reduzir-ao-limite aplica-se à reconciliação inicial. Ajustar o mód. 40 §2 aos dois casos.

**Entra no portão?** **Sim** — regressão introduzida pela criação do §18.7.2.

---

### ARB4-004 · Critério de aceite da Fase 1 inatingível: cinco das sete transições de cobertura exigem backend

**Origem:** Verificação 3 · **Severidade: Alta** · **Doc 5, Fase 1 (critérios) · Doc 2 §11.2**

**Problema.** O critério exige *"**todas as transições da tabela de cobertura (Documento 2, §11.2) têm teste**"*. A tabela tem sete linhas. O escopo da mesma fase fixa `COBERTURA_INDISPONIVEL`, que a linha 7 declara *"mutuamente exclusivo dos demais estados"*, e "Fora do escopo" exclui backend. Resultado:

| Linha | Alcançável na Fase 1? |
|---|---|
| 1 `sessao_ativada_sem_registro` → LOCAL | Não — estado excluído pela linha 7 |
| 2 `registro_confirmado_pelo_servidor` | Não — exige servidor |
| 3 `silencio_de_comunicacao_excedido` | Não — exige ter estado COMPLETA |
| 4 `comunicacao_restabelecida` | Não — exige servidor |
| 5 `perda_de_capacidade_de_disparo_local` (de LOCAL\|SUSPENSA) | Não — nenhum dos dois existe na fase |
| 6 `sessao_encerrada` | Sim |
| 7 `ativacao_em_build_sem_backend` | Sim |

**Consequência.** A fase que começaria nesta semana tem critério de aceite que não fecha: ou o agente simula servidor para exercitar as linhas 2 a 4 — proibido pelo núcleo §5 e pelo §18.7.2, que diz explicitamente *"sem simular servidor"* —, ou declara o critério atendido com duas de sete, que é a violação mais grave do núcleo §3.2. É o casamento de duas correções aprovadas isoladamente: a do item 4 fixou o estado, a do item 6 criou a linha 7, e ninguém escopou o critério.

**Recomendação.** Trocar por *"todas as transições da tabela de cobertura **alcançáveis nesta fase** (linhas 6 e 7 de §11.2) têm teste; as linhas 1 a 5 são obrigação da Fase 2, onde o servidor existe"*, e acrescentar a obrigação correspondente ao critério da Fase 2. Uma linha em cada fase.

**Entra no portão?** **Sim** — regressão produzida pela combinação das correções dos itens 4 e 6.

---

### ARB4-005 · Módulo 40 §7 carrega três gates na forma pré-correção

**Origem:** Verificação 5 · **Severidade: Alta** · **mód. 40 §7 contra núcleo §11**

**Problema.** O núcleo §11 foi reescrito nos três gates. O mód. 40 §7 mantém os três antigos:

| Gate | Núcleo §11 (vigente) | Mód. 40 §7 |
|---|---|---|
| Listas canônicas | origens **derivadas dos marcadores presentes**, §51 fora por ser remissão | lista fixa de quatro origens, **com o §51 dentro** |
| Marcadores | ao menos uma na seção citada; outras permitidas; cadeia literal; exceções | *"conferidas contra as tabelas do núcleo §0"*, sem nenhuma das quatro |
| `modo_teste` | invariante de ponto único de despacho tipado | *"nenhum caminho de notificação lê payload sem consultar `modo_teste`"* — análise de fluxo |

**Consequência.** O mód. 40 é o módulo que o agente carrega para tarefa de teste e de cobertura, e os gates são *"implantados antes da primeira linha de código de produção"*. Um agente que os implemente a partir do mód. 40 §7 constrói as três versões que foram reescritas por não poderem operar — inclusive a de marcadores, que a ARB3 mostrou reprovar o build em dez pontos legítimos. Aparece no primeiro PR de infraestrutura, antes da Fase 1.

**Recomendação.** Remissão pura ao núcleo §11, que é o que o **mód. 50 §11 já faz**: *"A lista de gates automatizados é a do núcleo §11."* Dois módulos do mesmo documento tratam o mesmo objeto com políticas opostas; um remete e o outro copia, e a cópia está velha.

**Entra no portão?** **Sim** — regressão introduzida pelas correções da terceira rodada, que reescreveram o núcleo e não atualizaram a cópia.

---

### ARB4-006 · A cláusula do §0 não alcança o próprio núcleo

**Origem:** Verificação 3 · **Severidade: Alta** · **núcleo §0 e §11 · mód. 40 §3 e §7 · mód. 50 §11**

**Problema.** A cláusula cobre reenunciação dos Documentos 2, 3 e 5. Nada cobre reenunciação **do núcleo pelos módulos**, e o gate de listas geradas só conhece origens nos Documentos 2 e 3 (quatro hoje). O ARB4-005 é a instância; o mód. 40 §3 reenunciando pisos de cobertura é a próxima.

**Consequência.** Todo gate, limiar e regra de evidência que vive no núcleo pode divergir num módulo sem detecção — e o módulo é o que o agente carrega. A hierarquia numerada não resolve, porque núcleo e módulo são o mesmo documento.

**Recomendação.** Estender a cláusula a "conteúdo do próprio núcleo" e acrescentar `00-nucleo.md, §11` como quinta origem do gate. Ou tornar a remissão obrigatória, no padrão do mód. 50 §11.

**Entra no portão?** Não — achado novo. O ARB4-005 já carrega a instância.

---

### ARB4-007 · A Fase 0 não mede o custo do mecanismo que a Fase 0 existe para validar

**Origem:** Verificação 2 (item 10) e Verificação 4 · **Severidade: Alta** · **Doc 5, Fase 0 (medições e critérios) · Doc 2 §16.8.3**

**Problema.** Duas ausências na lista canônica de medições da Fase 0:

1. **O `VERIFICAR:` do §16.8.3 é atribuído à Fase 0 e não tem linha lá.** O texto diz: *"`VERIFICAR:` confirmar em fonte oficial e em aparelho, na Fase 0, que `setUserAuthenticationParameters` com tempo zero implica autenticação por operação criptográfica e não por sessão de chave — toda a garantia contra pré-assinatura repousa nisso"*. A tabela de medições da Fase 0 não tem essa linha, e nenhum critério de aceite a cobra. A verificação mais carregada da suíte está alocada a uma fase que não a lista — e o Documento 5 é a fonte única de escopo e evidência pelo núcleo §0.
2. **Latência e custo da autenticação, e taxa de confirmação.** A tabela mede *"atraso de disparo do pedido de confirmação"* — o disparo do **pedido**. Não mede o tempo do prompt até assinatura verificada, o custo de bateria de N confirmações por sessão, nem a **taxa de confirmação dentro do prazo**.

**Consequência.** O ADR-0012 fecha os limiares de falso positivo com dado da Fase 0. O falso positivo **externo** — o único que bloqueia release, pelo mód. 40 §6 — é governado pela fração de usuários que não confirma a tempo, e o mecanismo do §16.8 exige biometria a cada confirmação, várias vezes por sessão, com o celular no bolso, no metrô, com a mão molhada. Esse número não está na lista do que a Fase 0 mede, e a Fase 0 exige *"método de medição escrito e revisado antes da coleta"* — acrescentar depois custa recoleta.

**Recomendação.** Três linhas na tabela da Fase 0 e um critério: latência do prompt até assinatura verificada, p50/p95/p99 por fabricante, com biometria **e** com credencial de aparelho; custo de bateria de N confirmações por sessão na cadência provisória; taxa de confirmação dentro do prazo sob as condições adversas que a matriz já nomeia, com motivo de falha registrado, declarada como insumo do ADR-0012. E fechar o `VERIFICAR:` do §16.8.3 com a fonte da Verificação 4 acima, mantendo apenas a confirmação em aparelho como linha da Fase 0.

**Entra no portão?** Não — backlog. É lacuna de escopo de fase, não regressão das correções.

---

### ARB4-008 · `MasterKeys` do Jetpack proíbe chave auth-per-use, e nada diz isso

**Origem:** Verificação 4 (item 3) · **Severidade: Média** · **mód. 30 §3 e mód. 10 (não recebidos) · Doc 2 §14.3**

**Problema.** Toda a garantia do §16.8 depende de `K_confirmacao` ser auth-per-use. O `androidx.security.crypto` **bloqueia** esse formato pela via de `MasterKeys`: foi acrescentada verificação que exige duração de validade maior que zero quando a autenticação é requerida. O §14.3 especifica os parâmetros corretos, e nenhum texto que eu recebi proíbe o caminho do Jetpack.

**Consequência.** Um agente que siga o caminho "recomendado" do Jetpack cria chave **baseada em tempo**, e a garantia central desaparece sem nenhum teste falhar: a asserção *"uma autenticação produz exatamente uma assinatura"*, exigida pela Fase 1, **passa** numa chave de janela curta se o teste assinar uma vez. É o modo de falha mais silencioso da suíte.

**Recomendação.** Proibir `MasterKeys` e qualquer wrapper que imponha duração > 0 para `K_leitura` e `K_confirmacao`. E acrescentar ao mód. 40 §2 o teste que fecha o buraco: **segunda assinatura sem nova autenticação deve falhar**. Provar que a primeira funciona não prova nada.

**Entra no portão?** Não — backlog. (Verificar em mód. 30 §3, que não recebi: se a proibição já estiver lá, este achado cai.)

---

### ARB4-009 · Invalidação de `K_confirmacao` com sessão ativa não tem comportamento declarado

**Origem:** Verificação 2 (item 9) · **Severidade: Alta** · **Doc 2 §14.3 · Doc 5, Fase 1 (critérios)**

**Problema.** O §14.3 tem a regra geral (*"Invalidação detectada emite `local_key_invalidated`, envia diagnóstico e solicita revinculação. **Nunca falha em silêncio**"*) e a Fase 1 tem o teste *"novo cadastro biométrico com sessão ativa"*. Mas o critério de aceite cobre duas chaves das três:

> *"`K_dados` sobrevive a novo cadastro biométrico; `K_leitura` é invalidada e o app pede revinculação sem falhar em silêncio"*

`K_confirmacao` não aparece. E é a que importa: invalidada, o usuário **não consegue mais confirmar** um prazo que o servidor já tem armado.

**Consequência.** O usuário cadastra uma digital nova numa terça-feira, com sessão ativa. `K_confirmacao` é invalidada, a confirmação seguinte não é assinável, o prazo vence, o vigilante dispara e o contato é acionado. Não é cenário adversarial — é manutenção rotineira de aparelho. Falta a regra: detectar e recriar a chave com step-up, reregistrando a pública, **ou** levar a sessão a estado degradado declarado com texto verdadeiro; e, nos dois casos, o que acontece com o prazo já registrado no servidor.

**Recomendação.** Uma alínea no §14.3 com o comportamento nos dois ramos, e a linha correspondente no critério da Fase 1 ao lado das outras duas chaves. A Fase 0 já mede o efeito observado, inclusive em chave que aceita credencial de dispositivo — a medição existe, a regra não.

**Entra no portão?** Não — backlog. A lacuna precede as correções da terceira rodada.

---

### ARB4-010 a ARB4-016 · Restantes

| ID | Origem | Sev. | Onde | Problema | Correção | Portão |
|---|---|---|---|---|---|---|
| **010** | V3 | Média | Doc 2 §39 | A partição do ADR-0005 deixou três parâmetros sem bloco: `grace_seconds` provisório (§12.5, termo 2), `janela_de_reconciliacao` de 180 s (§12.5, termo 6) e o teto do termo 9. O 0005-A enumera cinco itens e nenhum é esses; o 0005-B só tem `margem_de_rede`. O 0005-A é pré-condição da Fase 1 e pode ser aceito sem fechá-los | Acrescentar os três ao 0005-A | **Sim** — regressão da partição (Média não bloqueia a Decisão A) |
| **011** | V1 (backlog) | Alta | Doc 2 §16.3 | A coluna "Método e caminho" do ARB3-018 existe e está **vazia em 9 das 15 linhas** — `Heartbeat`, `Acionamento manual`, `Confirmação e encerramento de protocolo`, `Ciência do contato`, `Simulação`, `Convite`, `Registro de token`, `Recuperação`, `Billing`. Essas linhas têm 3 células numa tabela de 4 colunas: o mecanismo renderiza na coluna de caminho e a retenção some. O gate de idempotência do núcleo §11 precisa mapear rota → linha, e para 9 rotas não há rota | Preencher as 9 células com os caminhos do §21.2 | Não — item de backlog, declarado como aplicado |
| **012** | V3 | Média | núcleo L2 e L6; Doc 5 L5 | Cabeçalhos velhos. O núcleo — o arquivo que "deve estar **sempre** no contexto" — tem título "## Núcleo — versão 2.1" com `Versão: 2.3`, e a nota "Alteração da 2.2" descreve a segunda rodada, sem citar nenhuma correção da terceira (gates reescritos, 0005-B, cláusula alcançando o Doc 5, resíduo do §3.1). Doc 5 v2.3 tem a mesma nota da 2.2 | Corrigir título e reescrever as duas notas | Não — Média |
| **013** | V1 (item 3) | Média | núcleo §11 | O gate de marcadores promete *"lista de exceções explícita no próprio gate"* e não a contém. As ocorrências do núcleo §§4, 5, 7 e do Doc 2 §1 são texto que *fala sobre* o marcador e precisam dela | Enumerar as exceções | Não — o item 3 confere; é resíduo |
| **014** | V4 (item 1) | Baixa | Doc 2 §12.4 | O efeito está certo; o mecanismo não é o descrito. Pela documentação de plataforma, a permissão é concedida por padrão a todos os apps em Android 14 e é a **Play Store que a revoga na instalação** para quem não tem chamada ou despertador | Corrigir o mecanismo e exigir que o teste de tela cheia rode em build **instalado pela Play** (faixa interna), nunca por APK lateral | Não |
| **015** | V4 (varredura) | Baixa | mód. 50 §8 | **Correção de achado meu.** Havia apontado a política de contatos como ausente; ela está decidida no §34.5 ("evitada: entrada manual ou seletor do sistema"). Resta que o checklist de publicação não tem linha confirmando que nenhuma permissão de contatos é pedida — claim que regride em silêncio no dia em que alguém acrescentar "importar dos contatos" | Uma linha no checklist, com a data de vigência de 28/10/2026 registrada | Não |
| **016** | V3 | Alta condicional | Doc 2 §16.3 | `DELETE /me` retém `(chave, usuário, hash do corpo, resposta)` por **30 dias** para devolver "o estado do pedido já registrado". Isso mantém registro vinculado ao usuário por 30 dias **após o pedido de exclusão**. Não recebi o Doc 3 §32.3 para confrontar | Conferir contra §32.3: se a exclusão purga tudo, a garantia de idempotência quebra; se não purga, a retenção precisa de base legal declarada e entrada em Data Safety | Não — não verificável com o material recebido |

Registro também, sem número: §10 abre com *"São **duas**"* máquinas e *"Esta seção é a única fonte de estados do projeto"*, enquanto o §11.2 hospeda a terceira e o mód. 40 §3 exige *"as três máquinas"*. E o §39 lista o ADR-0013 antes do 0012, quebrando a ordem sequencial da própria tabela. Os dois são triviais e não gerei achado.

---

## RESUMO

```
## Verificação 1
Item 1 ARB3-001 (nonce):            NÃO EMITÍVEL — confere em Doc 2 e Doc 5;
                                     falta mód. 10 §8, mód. 20 §6, mód. 30 §4
Item 2 ARB3-002 (idempotência):     CONFERE
Item 3 ARB3-003 (gate marcadores):  CONFERE — gate testado contra o corpus, passa
Item 4 ARB3-004 (Fase 1 cobertura): CONFERE
Item 5 ARB3-005 (SEM_CIENCIA):      CONFERE
Item 6 ARB3-006 (COB_INDISPONIVEL): CONFERE
Item 7 ARB3-007 (listas geradas):   NÃO EMITÍVEL — confere no núcleo §11;
                                     falta o bloco em mód. 20 §6
Item 8 ARB3-008 (glossário):        CONFERE
Item 9 ARB3-009 (entrada Fase 1):   CONFERE

Backlog da terceira rodada aplicado: 10 de 11 — falta: ARB3-018 (ver ARB4-011)

## Verificação 2 — §16.8
Dos 10 pontos de ataque: fechados 6 | não fechados 4 (itens 1, 3, 5, 6)
Veredito do mecanismo: SÓLIDO COM AJUSTES
Concordo com os cinco descartes registrados na seção.

## Verificação 4 — plataforma
confirmadas 2 | incorretas 0 | não verificáveis 0
+ nenhuma afirmação nova incorreta nos 5 documentos recebidos
Semântica de setUserAuthenticationParameters com tempo zero: POR OPERAÇÃO
→ o VERIFICAR: do §16.8.3 pode ser fechado com fonte oficial

## Achados
Total: 16  | no portão: 6  | backlog: 10
Por severidade: Crítica 1  Alta 7  Média 4  Baixa 2  (+1 condicional)

## Itens no portão
ARB4-001  Crítica  regressão §16.8 + Crítico na Verificação 2
ARB4-002  Alta     regressão §16.8
ARB4-003  Alta     regressão §18.7.2
ARB4-004  Alta     regressão (itens 4 + 6 combinados)
ARB4-005  Alta     regressão (núcleo §11 reescrito, mód. 40 §7 não)
ARB4-010  Média    regressão (partição do ADR-0005)

## DECISÃO A — congelamento da documentação
❌ NÃO CONGELAR

O critério desta rodada nomeia exatamente este caso: existe achado Crítico
na Verificação 2 (ARB4-001) e existem quatro regressões Altas introduzidas
pelas correções da terceira rodada. Não reclassifico para chegar a um
veredito mais confortável — é a manipulação que apontei na seção 0.

Mas o conteúdo do que falta é pequeno, e isso importa para a sua decisão:
dos seis itens do portão, cinco são uma frase ou um parágrafo, e o sexto
(ARB4-005) é apagar três linhas do mód. 40 §7. Nenhum é redesenho. O único
que passa perto de decisão é o ARB4-001, e ele cabe numa alínea do
ADR-0013, que ainda não existe — logo não há ADR a emendar.

Se você quiser mudar o critério para "CONGELAR APÓS AJUSTES", essa é uma
decisão sua e legítima, tomada às claras. O que eu não faço é chegar lá
reclassificando achado.

Minha recomendação operacional: aplicar os seis, mais os quatro Médios,
e **não** rodar quinta rodada completa. A quinta verificação é conferência
no diff, mais os três módulos que faltam (10, 20, 30) contra o §16.8 —
que é o único ponto onde eu ainda esperaria achado, pelo padrão do ARB4-005.

## DECISÃO B — qual fase pode iniciar agora
✅ FASE 0 pode iniciar

As dependências declaradas são hardware, servidor de teste, conta no Play
Console, contas em três operadoras, e ADR-0001, 0002 e 0003 aprovados —
todos "antes da Fase 0" e nenhum dependente desta auditoria. Nada nos 16
achados a bloqueia, e quatro deles (ARB4-007, 009, e os pontos 1 e 9 da
Verificação 2) só se resolvem com número que ela mede.

Uma condição, por causa da própria regra da fase: acrescente as três linhas
do ARB4-007 à tabela de medições **antes** de escrever o método. A fase
exige método revisado antes da coleta, então medição acrescentada depois
custa recoleta em aparelho físico.

❌ FASE 1 não pode iniciar

Faltam as pré-condições que a própria fase declara: ADR-0004, ADR-0005-A e
ADR-0013 não existem, e a decisão da tela de bloqueio não foi tomada nem a
alternativa de escopo foi eleita. Quem produz: o fundador — são quatro
decisões, e o ADR-0013 agora precisa incorporar as correções dos ARB4-001
e 002. Some-se o ARB4-004: com o critério como está, a fase não fecha nem
depois dos ADRs.

**A Fase 0 sozinha não produz nenhuma das quatro.** Ela produz insumo para
duas — o dado de mercado da tela de bloqueio e o p99 do ADR-0005-B — e nada
para o ADR-0004 e o ADR-0013, que são decisões de desenho já disponíveis.
Os dois podem ser escritos esta semana, em paralelo à Fase 0.
```

---

## Uma observação sobre o processo, que vale mais que qualquer item acima

Cinco dos seis itens do portão são **regressões de acoplamento**: correções individualmente certas que se contradizem quando lidas juntas. O item 4 fixou o estado de cobertura da Fase 1; o item 6 criou a linha 7; nenhum estava errado, e juntos produziram um critério de aceite inatingível. O mesmo padrão produziu o ARB4-003 (§18.7.2 contra §18.7 item 2a) e o ARB4-005 (núcleo reescrito, módulo não).

Três rodadas seguidas encontraram esse padrão, e ele não é falha de quem corrige: é o que acontece quando 14 documentos com remissão cruzada mudam por lote. Auditar mais não conserta — o que conserta é o gate de listas geradas que vocês já têm, aplicado ao que ainda não cobre: o núcleo, o Documento 5 e as tabelas que outras seções consomem. O ARB4-006 é esse conserto, e é o item de backlog que eu subiria de prioridade acima de todos os outros.
