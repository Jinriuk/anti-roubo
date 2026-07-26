# Verification Board — terceira rodada (ARB3)
## Modo Rua — verificação de congelamento

**Data:** 26/07/2026 · **Material recebido:** 13 de 14 arquivos (a instrução de correção do fundador não existe como documento; ver nota de escopo) · **Achados:** 21 · **Decisão:** ❌ REPROVADO

---

## Nota de escopo, primeiro

**Discordo de uma parte da regra de escopo.** A regra manda que achado novo de severidade Alta que não seja regressão vá para o backlog. Aceito e aplico. Mas a regra classifica pelo *destino* e decide pelo destino, e a fronteira entre "regressão" e "novo" é o único juízo desta rodada que muda o resultado — enquanto o próprio prompt informa ao revisor que reprovar é caro e aprovar é barato. Quem quiser aprovar reclassifica, sem mentir uma vez. Registro a discordância como item próprio (ARB3-021) e aplico a regra como escrita.

**Cobertura desta rodada.** Executei as verificações 1, 2, 3, 5, 6, 7, 8, 9 e 10. **Não executei a verificação 4** (as 18 afirmações de plataforma contra fonte oficial): cinco delas são datadas e posteriores ao meu corte de conhecimento, e re-verificá-las exige busca com fonte oficial que não fiz nesta passagem. As 13 restantes reproduzem fielmente os vereditos que a ARB2 já sustentou com fonte citada — conferi §35, §12.2, §34.5, §4.7 e §12.4 contra a Parte 1 do relatório ARB2 e o texto corrigido corresponde, inclusive nas duas que estavam erradas e foram consertadas. Isso **não** substitui a verificação 4. Ela fica como item obrigatório da próxima rodada, e a decisão abaixo não depende dela.

**Ausência da instrução de correção.** Sem ela, MOD-01, MOD-02 e ADD-01 a ADD-07 (9 de 42 itens) foram verificados contra o que o changelog **declara**, não contra o que foi **pedido**. Para MOD-01 e MOD-02 isso é parcialmente recuperável, porque correspondem aos achados ARB2-021 e ARB2-029, cujos enunciados eu tenho. Para ADD-02, ADD-03, ADD-05 e ADD-07 a verificação é apenas de coerência interna.

---

# PARTE 1 — Os nove itens de bloqueio

| ID | Veredito | O que sustenta |
|---|---|---|
| **ARB2-001** | **Corrigido** | §14.3 reescrito com três chaves; `K_leitura`/`K_confirmacao` com `BIOMETRIC_STRONG \| DEVICE_CREDENTIAL`; "O PIN interno não é chave e não desbloqueia chave", fallback é o credencial do aparelho; proibição espelhada em §41 e mód. 30 §3; exigência de tela de bloqueio corretamente `[PENDENTE]`. O contador circular desapareceu. |
| **ARB2-002** | **Corrigido** | §14.3 coloca a fila pendente, "incluindo payload de amostras de localização ainda não sincronizadas", sob `K_dados`; §15 declara a chave por coluna (`latitude_encrypted (K_dados)`); teste da Fase 1 exige worker sincronizando "sem qualquer autenticação do usuário". |
| **ARB2-003** | **Corrigido** | §18.7 item 2a limita `expected_next_checkin_at` **e** `grace_seconds` por `policy_version`, com aumento válido só na confirmação seguinte e redução imediata; §22.3 inclui parâmetro de temporização no step-up; evento `SECURITY` mais canal externo. |
| **ARB2-004** | **CORRIGIDO PELA METADE** | Ver ARB3-001. A verificação no servidor está especificada e `confirmation_type` passou a ser derivado. Falta o mecanismo que torna a assinatura produzível fora de rede — e é a maior parte do envelope operacional do produto. |
| **ARB2-005** | **Corrigido** | §11.1 dá texto próprio a `COBERTURA_SUSPENSA` ("O aviso aos seus contatos segue armado pelo prazo já registrado"); a distinção "o servidor sabe da sessão?" está explícita; bloqueador ampliado para texto que não corresponde ao estado. |
| **ARB2-006** | **CORRIGIDO PELA METADE** | Ver ARB3-002. A tabela foi de 4 para 12 linhas e cobre os 11 endpoints nomeados pela ARB2, com gate novo. Mas a regra é absoluta ("toda operação mutante") e três operações mutantes continuam sem linha. |
| **ARB2-007** | **CORRIGIDO PELA METADE** | Ver ARB3-003. Os marcadores agora existem em §34.5 e na Fase 0, a tabela do núcleo §0 existe e o gate existe. O gate, como especificado, reprova o build contra o próprio corpus que governa. |
| **ARB2-016** | **Corrigido** | §4.7 e §14.4 exigem os dois mecanismos, com `<device-transfer>` explícito e fonte citada; gate de manifesto no núcleo §11; testes por nuvem no `minSdk` **e** por D2D na Fase 1 e no mód. 40 §5. |
| **ARB2-032** | **CORRIGIDO PELA METADE** | Ver ARB3-004 e ARB3-006. `COBERTURA_INDISPONIVEL` existe com texto próprio e o critério da Fase 1 foi reescrito. Mas o **escopo** da mesma Fase 1 continua mandando o estado ser `COBERTURA_LOCAL`, e o estado novo não tem linha na tabela canônica do §11.2. |

**Placar: 5 corrigidos · 4 corrigidos pela metade · 0 não corrigidos.**

Aplicando o critério de suficiência do prompt ("um agente competente, lendo apenas os documentos, consegue implementar sem inventar mecanismo?"): nos quatro casos acima a resposta é não.

---

# PARTE 2 — Achados que impedem o congelamento

## ARB3-001

**Destino:** Verificação (e regressão, pela mesma correção)
**Veredito:** Corrigido pela metade
**Severidade:** **Crítica**
**Documento e localização:** Documento 2, §16.8 · Documento 4, mód. 30 §4 · Documento 5, Fase 1 (escopo e critérios de aceite)

**Problema.** A correção do ARB2-004 define a assinatura de confirmação sobre a tupla `(installation_id, session_id, check_in_id | protocol_id, **nonce do servidor**, expected_next_checkin_at)`. A mesma tupla, com o mesmo `nonce do servidor`, é repetida no mód. 30 §4. **Nenhum documento diz como o aparelho obtém esse nonce sem rede.** Varri os 13 arquivos: "nonce" aparece cinco vezes, sempre como requisito, nunca com mecanismo de emissão, cache, lote ou substituto.

Isso colide com três coisas que os documentos afirmam:

1. **Núcleo §2.2:** "ativar, registrar, temporizar e sobreviver a reinício **nunca dependem de rede**". A confirmação de check-in é o ato central da temporização.
2. **§11.1, `COBERTURA_SUSPENSA`:** estado definido como "comunicação interrompida", cujo texto obrigatório agora promete que "o aviso aos seus contatos segue armado". É o estado do metrô, e é onde o usuário legítimo precisa confirmar.
3. **Documento 5, Fase 1:** o critério de aceite exige que a confirmação forte "é **assinada** por `K_confirmacao`", e a mesma fase põe o backend **fora do escopo**. Na Fase 1 não existe servidor, logo não existe nonce, logo a assinatura especificada no §16.8 não é produzível.

As duas saídas que sobram para o agente estão ambas fechadas pelos documentos: reusar nonce é proibido explicitamente (mód. 30 §3 e Documento 3 §27.5 proíbem "IV ou nonce reutilizado"), e inventar emissão em lote é inventar mecanismo.

**Consequência.** Na **Fase 1** o critério de aceite é inatingível como escrito — o agente entrega uma assinatura com nonce fictício e a marca como pronta, que é o modo de falha que o Documento 4 existe para impedir, ou para e abre issue. Na **Fase 2 em diante** a colisão é pior: ou o servidor aceita confirmação sem nonce, e o ARB2-004 é desfeito na prática, ou a confirmação offline não se conclui, a graça expira, o vigilante encontra o prazo vencido e **aciona o contato de um usuário que confirmou corretamente**. Isto é: a correção que tornou a confirmação verificável transformou o cenário-bandeira do produto em falso positivo garantido.

**Recomendação.** Decidir e escrever uma das três, por ADR (é decisão estrutural, não edição de texto):
- **lote de nonces de uso único**, emitido no registro da sessão (§18.7, item 1) e reposto a cada comunicação bem-sucedida, dimensionado para o número esperado de check-ins da sessão mais margem; declarar o comportamento quando o lote esgota offline;
- **assinatura sobre `sequence`** em vez de nonce, com proteção de replay no servidor por monotonicidade de `(installation_id, sequence)` — encaixa no modelo que o §16.2 já tem e dispensa rede;
- **escopo reduzido**: nonce apenas nos três eventos que já são naturalmente online (encerramento autenticado de sessão, encerramento de protocolo, alteração de parâmetro de temporização, que exige step-up), e mecanismo próprio, sem nonce, para a confirmação de check-in.

Em qualquer das três, corrigir o critério da Fase 1 para descrever o que é verificável sem backend.

**Justificativa.** Um nonce é, por definição, emitido pelo verificador e de uso único. Amarrar a ele o único ato que o produto precisa executar sem rede é uma contradição de requisito, não um detalhe de implementação. O §16.8 aplica um mecanismo único a quatro tipos de evento com premissas de conectividade opostas.

**Bloqueia o congelamento?** **Sim.** Regressão Crítica, introduzida por esta correção, com efeito direto no escopo da Fase 1.

---

## ARB3-002

**Destino:** Verificação
**Veredito:** Corrigido pela metade
**Severidade:** Alta
**Documento e localização:** Documento 2, §16.3 · §21.2 · Documento 4, núcleo §11

**Problema.** §16.3 abre com regra absoluta: "Toda operação mutante tem linha nesta tabela. Endpoint mutante sem linha é PR reprovada". Continuam sem linha:
- `PATCH /installations/{id}` (§21.2);
- `DELETE /me` — exclusão de conta, que é entregável da Fase 2 e item de checklist de publicação;
- **alteração de parâmetro de temporização** — operação criada por esta rodada (§18.7 item 2a, §22.3, §16.8), que é mutante, exige step-up, é assinada e gera evento `SECURITY`, e que **não tem nem endpoint em §21.2 nem linha em §16.3**.

**Consequência.** O gate novo de idempotência (núcleo §11) reprova o build no primeiro PR da Fase 2 que implemente qualquer um dos três, e o agente não tem linha para consultar: inventa chave e retenção. A operação de temporização é a mais grave das três, porque é a que o ARB2-003 acabou de criar como superfície de ataque controlada.

**Recomendação.** Três linhas em §16.3 e um endpoint em §21.2. Sugestão para a de temporização: `PATCH /street-mode/sessions/{id}/timing`, `Idempotency-Key` do cliente, retenção 24 h, com nota de que a resposta devolve o `policy_version` vigente para o aparelho não aplicar localmente valor que o servidor rejeitou.

**Bloqueia o congelamento?** **Sim.** Item de bloqueio não fechado.

---

## ARB3-003

**Destino:** Verificação e regressão
**Veredito:** Corrigido pela metade
**Severidade:** Alta
**Documento e localização:** Documento 4, núcleo §0 (tabelas) e §11 (gate "Marcadores abertos")

**Problema.** O gate diz: "Ocorrências de `[ABERTO — FASE 0]` e de `[PENDENTE — DECISÃO DO FUNDADOR]` em `docs/` conferem com as tabelas do §0, por seção citada. Divergência falha o build." Contei as ocorrências no corpus entregue:

`[ABERTO — FASE 0]` — a tabela do §0 cita 4 locais (Doc 2 §12.2, §34.5, §18.7, Doc 5 Fase 0). Ocorrências reais fora desses locais: Documento 2 **§12.5** (termo 4 do orçamento), Documento 3 (1), mód. 10-android (3), mód. 20-backend (1), mód. 30-seguranca (1).

`[PENDENTE — DECISÃO DO FUNDADOR]` — a tabela cita 3 locais (Doc 2 §14.3, §10.2, §10.4). Ocorrências reais fora deles: Documento 2 **§10.1 linha 10a**, Documento 3, Documento 5 Fase 1, mód. 10-android, mód. 30-seguranca.

**Consequência.** O núcleo diz que os gates são "implantados antes da primeira linha de código de produção". Este reprova o build na implantação, em pelo menos dez pontos, e nenhum deles é erro de documentação: são remissões legítimas ao item. É o perfil exato de gate que o fundador desliga na primeira semana — e um gate desligado é pior que ausente, porque a tabela do §0 passa a parecer conferida.

Some-se: `margem_de_rede` aparece na tabela com **dois** ADRs donos ("0005 / 0007"), sem regra de qual fecha. O mód. 50 §5 exige ADR para fechar item aberto; o agente não sabe qual propor.

**Recomendação.** Reescrever a regra do gate: cada item da tabela do §0 tem **ao menos uma** ocorrência na seção citada (falha se faltar); ocorrências em outras seções são permitidas e devem citar o item dono; ocorrências dentro de texto que *fala sobre* o marcador ficam fora da contagem por lista explícita. Fixar a cadeia exata (travessão, não hífen) como literal do gate. Atribuir um único ADR dono a `margem_de_rede`.

**Bloqueia o congelamento?** **Sim.** Item de bloqueio não fechado, e o controle que o fecha está especificado de forma que não pode entrar em operação.

---

## ARB3-004

**Destino:** Verificação e regressão
**Veredito:** Corrigido pela metade
**Severidade:** Alta
**Documento e localização:** Documento 5, Fase 1 — "Escopo" contra "Critérios de aceite"

**Problema.** O escopo da Fase 1 diz: "Estado de cobertura visível, **sempre `COBERTURA_LOCAL` nesta fase** (Documento 2, §11.1)". Onze linhas abaixo, o primeiro critério de aceite diz: a interface usa "o texto próprio de build sem backend do Documento 2 §11.1 — **não** com o texto de `COBERTURA_LOCAL`". A correção do ARB2-032 consertou o critério e não tocou o escopo.

**Consequência.** O escopo vem antes na leitura e é o que o agente usa para planejar. Ele implementa `COBERTURA_LOCAL`, cujo texto obrigatório em §11.1 é exatamente a frase falsa que os ARB2-005 e ARB2-032 existiram para eliminar. Pior: o gate de texto ("o texto exibido corresponde ao estado") **passa**, porque o texto corresponde ao estado implementado. O controle não pega o defeito.

**Recomendação.** Trocar a linha de escopo para "sempre `COBERTURA_INDISPONIVEL` nesta fase".

**Bloqueia o congelamento?** **Sim.** Item de bloqueio não fechado, na fase que começaria nesta semana. Conferível no diff.

---

## ARB3-005

**Destino:** Regressão
**Severidade:** Alta
**Documento e localização:** Documento 2, §10.2 (tabela de transições)

**Problema.** A linha 14 **entra** em `SEM_CIENCIA`. Nenhuma linha da tabela **sai** dele. As linhas 9, 10, 11 e 12 têm origem `ALERTANDO` ou `EMERGENCIA`; a 13 tem origem `RESOLVIDO`; `ciencia_do_contato` é declarada "sem mudança". O texto imediatamente abaixo afirma o contrário do que a tabela permite: "estado observável, **não terminal**, que não fecha o protocolo" e "mantém o protocolo aberto e **reversível**".

**Consequência.** O titular que volta, autentica e quer encerrar o protocolo não tem transição para tomar: a linha 12 não aceita `SEM_CIENCIA` como origem. O protocolo nunca alcança `RESOLVIDO`, logo a linha 13 nunca dispara, logo nunca retorna a `NORMAL`. O estado criado para *não* ser terminal é o único terminal absoluto da máquina. Aparece na Fase 4, e a obrigação de teste ("todas as transições") não pega, porque não há transição a testar.

**Recomendação.** Acrescentar `SEM_CIENCIA` como origem nas linhas 9, 10 e 12, e declarar o efeito de `ciencia_do_contato` recebida em `SEM_CIENCIA` (retorno a `ALERTANDO` ou permanência com registro — é escolha, e precisa estar escrita).

**Bloqueia o congelamento?** **Sim.** Regressão Alta. Conferível no diff.

---

## ARB3-006

**Destino:** Regressão
**Severidade:** Alta
**Documento e localização:** Documento 2, §11.2 (tabela de transições de `coverage_state`) e matriz de combinação

**Problema.** `COBERTURA_INDISPONIVEL`, criado pela correção do ARB2-032, **não aparece em nenhuma linha** da tabela do §11.2 nem na matriz "Sessão × Cobertura". O §11.2 foi criado pelo ARB2-017 justamente para que a obrigação de teste da Fase 1 alcançasse a cobertura, e o critério de aceite da Fase 1 exige "todas as transições da tabela de cobertura (§11.2) têm teste".

**Consequência.** O único estado de cobertura que existe na Fase 1 é o que não tem linha, não tem transição e portanto não tem obrigação de teste. Duas correções aplicadas sem serem conciliadas — o mesmo padrão que produziu o ARB2-005.

**Recomendação.** Declarar `COBERTURA_INDISPONIVEL` no §11.2 como constante de build (entrada por configuração de build sem backend, sem transições de saída, exclusivo dos demais estados) e acrescentar a linha correspondente na matriz de combinação.

**Bloqueia o congelamento?** **Sim.** Regressão Alta, escopo da Fase 1. Conferível no diff.

---

## ARB3-007

**Destino:** Regressão
**Severidade:** Alta
**Documento e localização:** Documento 4, núcleo §11 (gate "Listas canônicas") · mód. 20 §6 · mód. 30 §4 · mód. 10 §? (bloco de §12.3)

**Problema.** O gate declara quatro origens validadas: §51 (bloqueadores), §34.1 (eventos auditáveis), §20.3 (step-up), §18 (módulos do backend). Os blocos efetivamente marcados com `<!-- gerado de ... -->` no corpus são **três**, e não são esses quatro:

| Marcado no corpus | Está no gate? |
|---|---|
| Doc 2 §12.3 (mód. 10) | **Não** |
| Doc 2 §18 (mód. 20) | Sim |
| Doc 3 §34.1 (mód. 30) | Sim |
| Doc 3 §20.3 | **Não existe bloco marcado** |
| Doc 3 §51 | **Não existe bloco marcado** (mód. 30 remete, sem cópia — correto) |

Agrava: o mód. 20 §6 reenuncia a lista de step-up **à mão** e afirma no próprio texto que ela é "lista reenunciada **e validada em CI**". Não é: não há marcador para o CI validar. O mód. 30 §4 reenuncia a mesma lista, também sem marcador.

**Consequência.** É a classe de defeito que o MOD-01 e o ARB2-019 existiram para fechar, reintroduzida um nível acima: agora o documento **afirma** o controle. Uma reenunciação divergente do step-up passaria pelo CI e venceria por hierarquia (o Doc 4 está acima do Doc 2 e a cláusula do §0 cobre Docs 2 e 3 — aqui a origem é o Doc 3, então a cláusula salva o mérito, mas não a detecção). E o bloco de §12.3, que é o único marcado fora da lista do gate, pode divergir em silêncio.

**Recomendação.** Marcar os blocos de step-up no mód. 20 §6 e no mód. 30 §4 com `<!-- gerado de Documento 3, §20.3 -->`, ou substituí-los por remissão pura como o mód. 30 fez com o §51. Acrescentar §12.3 à lista do gate. Remover §51 da lista, ou qualificá-la como "validada quando houver cópia". Apagar a afirmação "validada em CI" de qualquer bloco que não carregue marcador.

**Bloqueia o congelamento?** **Sim.** Regressão Alta: controle afirmado e não implementado. Conferível no diff.

---

## ARB3-008

**Destino:** Regressão
**Severidade:** Alta
**Documento e localização:** Documento 2, §3 (glossário canônico)

**Problema.** O glossário, que o §3 declara canônico e de "termo único por conceito", continua definindo: "Confirmação forte | `strong_confirmation` | Confirmação autenticada por biometria **ou PIN interno**". O §14.3 corrigido diz o oposto: "O PIN interno não é chave e não desbloqueia chave", o fallback é o credencial do aparelho, e o papel do PIN é `[PENDENTE — DECISÃO DO FUNDADOR]`. Além disso, `K_confirmacao`, `SEM_CIENCIA` e `COBERTURA_INDISPONIVEL` — três identificadores canônicos criados nesta rodada — não estão no glossário.

**Consequência.** O glossário é o artefato que o agente consulta para saber o que uma palavra significa, e ele hoje autoriza a leitura que o ARB2-001 acabou de proibir. O mód. 50 §9 manda abrir issue quando termo abolido aparece; não há regra equivalente para termo canônico que ficou errado. A definição sobrevive porque o prompt desta correção declarou o §3 fora de escopo — a decisão de não mexer no glossário está correta como política e produziu o defeito neste caso concreto.

**Recomendação.** Reescrever a linha para "autenticada por biometria ou pelo credencial de tela de bloqueio do aparelho". Acrescentar as três entradas novas. É a única alteração necessária no §3.

**Bloqueia o congelamento?** **Sim.** Regressão Alta na fonte canônica de terminologia. Conferível no diff.

---

## ARB3-009

**Destino:** Regressão
**Severidade:** Alta
**Documento e localização:** Documento 5, Fase 1 (escopo, testes obrigatórios, critérios de aceite) · Documento 2, §14.3

**Problema.** A Fase 1 declara `[PENDENTE — DECISÃO DO FUNDADOR]` a exigência de tela de bloqueio segura, com "nenhum agente implementa nenhuma das opções". Ao mesmo tempo: exige o teste "aparelho sem tela de bloqueio segura (**comportamento conforme a decisão pendente**)", e o critério de aceite exige confirmação forte "assinada por `K_confirmacao`". `K_confirmacao` é chave com `setUserAuthenticationRequired(true)`: em aparelho sem tela de bloqueio segura ela não é criável. Na opção 2 da pendência (operar sem bloqueio, em modo degradado), o critério de aceite é inatingível por construção nessa classe de aparelho.

**Consequência.** A Fase 1 **não fecha** enquanto a pendência existir: tem um teste obrigatório sem comportamento esperado e um critério de aceite condicionado a uma decisão que ninguém tomou. E a decisão depende de dado que só a Fase 0 produz ("Parcela do público-alvo sem tela de bloqueio segura"). Nada em nenhum documento declara esse acoplamento — a pendência não está listada como pré-condição de entrada da Fase 1.

**Recomendação.** Declarar em Doc 5, Fase 1, que a decisão da tela de bloqueio é **pré-condição de entrada** do item de chaves e onboarding, e marcar quais critérios de aceite são condicionais a cada ramo. Alternativa, se a intenção for iniciar a Fase 1 já: escopar a Fase 1 apenas para aparelhos com tela de bloqueio segura, declarando isso como limitação da fase e não do produto.

**Bloqueia o congelamento?** **Sim.** Sem isso, "congelar e começar a Fase 1" significa começar uma fase que não pode ser concluída.

---

# PARTE 3 — Achados que não impedem o congelamento

| ID | Destino | Sev. | Localização | Problema em uma linha | Correção |
|---|---|---|---|---|---|
| ARB3-010 | Regressão | Média | Doc 2, §10.2 | A lista de estados da abertura não inclui `SEM_CIENCIA`; uma linha abaixo da tabela manda o leitor emendá-la ("acrescenta-se `SEM_CIENCIA` à lista da abertura"). Errata dentro da fonte canônica de estados. | Incluir na lista e apagar a errata. |
| ARB3-011 | Regressão | Média | Doc 2, §14.3 e §14.4 | A tabela de chaves diz que `K_leitura` protege "Histórico legível", e o ADD-02 duas linhas abaixo diz que o histórico legível vem do servidor e nada fica sob `K_leitura`. Sobram cofre e áreas seguras. | Remover "Histórico legível" da linha, ou qualificar como cache vindo do servidor. |
| ARB3-012 | Regressão | Média | Doc 2, §12.5 | O orçamento de latência tem oito termos e não inclui o `jitter` nem o reagendamento por indisponibilidade (§18.7.1), que somam ao caminho. A medição de ponta a ponta da Fase 4 tem como cenário obrigatório a "tempestade de recuperação" — mede um caminho que o orçamento não contém. | Termo 9 declarado, aplicável só ao caminho de recuperação, com teto próprio. |
| ARB3-013 | Regressão | Média | Doc 2, §10.4 e §10.1 linha 10a | A regra interina ("enquanto pendente, nenhum agente implementa `timeout` como transição terminal") é operacionalmente idêntica à opção A, que é também a recomendação registrada. A pendência está decidida de fato e rotulada como aberta. É o padrão do ARB2-007 (recomendação lida como decisão) em forma nova. | Declarar explicitamente: "default provisório = comportamento da opção A, sem prejuízo da decisão", para que o agente saiba que é interino e não escolha. |
| ARB3-014 | Regressão | Baixa | Doc 2, §34.5 | Duas linhas de `ACCESS_FINE_LOCATION` nomeiam a mesma política ("escopo mínimo"), uma com prazo "a redigir na Fase 0" e outra como gate da Fase 7. | Renomear a política da primeira linha para o que ela é (justificativa de funcionalidade central). |
| ARB3-015 | Regressão | Baixa | Doc 2, §39 | O prazo do ADR-0011 continua "Antes da Fase 3", enquanto a Fase 0 corrigida exige "ADR-0011 provisório proposto" e o mód. 50 §5 diz "proposto já na Fase 0". | Alinhar o §39: "provisório na Fase 0; definitivo antes da Fase 3". |
| ARB3-016 | Novo | Alta | Doc 4, núcleo §0 | A cláusula do MOD-01 retira do Doc 4 a autoridade sobre conteúdo dos **Documentos 2 e 3**. O Doc 4 continua acima do **Documento 5**, que é a fonte única de fases, e a cláusula não menciona escopo, fase, critério de aceite ou evidência. Os módulos 40 e 50 reenunciam alocação de fase em vários pontos. A classe de defeito que o MOD-01 fechou para arquitetura segue aberta para roadmap. | Estender a cláusula a "conteúdo de escopo, fase, critério de aceite e evidência (Documento 5)". Backlog da Fase 1. |
| ARB3-017 | Novo | Média | Doc 4, núcleo §11 | O gate `modo_teste` ("caminho de notificação que lê payload sem consultar a flag") exige análise de fluxo de dados. É disciplina humana com aparência de automação — o defeito que o ARB2-028 nomeou. | Trocar por invariante verificável: um único ponto de despacho tipado, e o gate falha se qualquer outro call site chamar o provedor direto. |
| ARB3-018 | Novo | Média | Doc 4, núcleo §11 · Doc 2, §16.3 | O gate de idempotência precisa mapear rota → linha, e a tabela do §16.3 é indexada por "Caso" em prosa, com linhas que cobrem dois endpoints. Sem coluna de endpoint, o mapeamento é manual. | Acrescentar coluna método + caminho ao §16.3. |
| ARB3-019 | Novo | Baixa | Doc 2, §10 | §10.4 está fisicamente entre §10.1 e §10.2. Numeração fora de ordem temática é decisão; fora de ordem **sequencial** no corpo é obstáculo de navegação para quem lê linearmente, que é o agente. | Mover para depois de §10.3. |
| ARB3-020 | Novo | Baixa | Doc 4, núcleo §3.1 | A correção do ARB2-028 derivou o rótulo dos caminhos, mas não incluiu a divulgação que o próprio achado recomendava: a **remoção** do rótulo continua sendo julgamento humano não verificável por máquina, e isso não está declarado. | Uma frase no §3.1 declarando o resíduo, como o Doc 3 §37.3 já faz para segregação de funções. |
| ARB3-021 | Novo | — | Este prompt | Discordância registrada da regra de escopo, conforme a Parte 0. | — |

---

# PARTE 4 — O que ainda falta para a primeira semana da Fase 1

1. **Mecanismo de nonce, ou substituto** (ARB3-001). É o primeiro item, e é o único da lista que não é edição de texto.
2. **ADR-0004 e ADR-0005**, ambos com prazo "antes da Fase 1" e nenhum existente. O ADR-0005 acumulou nesta rodada: parâmetros do vigilante, teto do §12.5, limites absolutos da guarda de anomalia, cadência de heartbeat, tetos de `ALERTANDO` e `silencio_maximo_de_comunicacao`. Alguns dependem de medição da Fase 0. Vale partir o ADR-0005 em dois, ou declarar quais parâmetros nascem provisórios e são revistos.
3. **Decisão da tela de bloqueio segura** (ARB3-009), que é pré-condição real da Fase 1 e depende de dado da Fase 0.
4. **Verificação 4 desta rodada** — as 18 afirmações de plataforma contra fonte oficial, com atenção às cinco datadas.
5. **Definição de `policy_version`**: aparece agora em três regras (limite de parâmetros, tetos de `ALERTANDO`, cadência de heartbeat) e nenhuma seção diz o que ele contém, como é versionado ou como o aparelho descobre os limites vigentes. Quem corrigiu registrou isso no changelog como candidato a achado; confirmo que é, e que aparece na Fase 2.

---

```
## Resumo executivo
Total de achados: 21
Por destino:  Verificação 4  Regressão 11  Novo 6
Por severidade:  Crítica 1  Alta 8  Média 6  Baixa 5 (+1 sem severidade)

Itens de bloqueio da segunda rodada:
  corrigidos 5 de 9 | corrigidos pela metade 4 | não corrigidos 0

Correções declaradas no changelog verificadas como efetivamente aplicadas: 34 de 42
  (33 achados: 29 aplicadas, 4 parciais — ARB2-004, 006, 007, 032 e a metade de
   ARB2-019 que virou ARB3-007. MOD-01, MOD-02 e ADD-01 a ADD-07: aplicadas
   conforme o changelog declara, verificação limitada pela ausência da instrução.)

Afirmações de plataforma introduzidas nesta rodada:
  confirmadas 0 | incorretas 0 | não verificáveis 0
  — verificação 4 NÃO EXECUTADA nesta rodada. 13 das 18 reproduzem fielmente
    vereditos que a ARB2 sustentou com fonte citada; 5 são datadas e exigem
    re-verificação com fonte oficial.
```

## Itens que impedem o congelamento

| ID | Destino |
|---|---|
| ARB3-001 | Verificação (ARB2-004, pela metade) + Regressão Crítica |
| ARB3-002 | Verificação (ARB2-006, pela metade) |
| ARB3-003 | Verificação (ARB2-007, pela metade) + Regressão |
| ARB3-004 | Verificação (ARB2-032, pela metade) + Regressão |
| ARB3-005 | Regressão Alta |
| ARB3-006 | Regressão Alta |
| ARB3-007 | Regressão Alta |
| ARB3-008 | Regressão Alta |
| ARB3-009 | Regressão Alta |

## Backlog da Fase 1

ARB3-016 (Alta) · ARB3-017 (Média) · ARB3-018 (Média) · ARB3-010 (Média) · ARB3-011 (Média) · ARB3-012 (Média) · ARB3-013 (Média) · ARB3-014 (Baixa) · ARB3-015 (Baixa) · ARB3-019 (Baixa) · ARB3-020 (Baixa)

## Decisão

❌ **REPROVADO**

Aplicando o critério do próprio prompt: existe regressão **Crítica** (ARB3-001) e ela atinge o escopo direto da Fase 1. Isso é suficiente, e é o único motivo que eu trataria como suficiente — os outros oito itens de bloqueio são todos conferíveis no diff e, sozinhos, dariam "aprovado com ajustes".

A reprovação é ainda mais estreita que a da segunda rodada. Oito dos nove itens são edição de texto: uma linha de escopo na Fase 1, três linhas na tabela de idempotência, um endpoint, linhas de origem para `SEM_CIENCIA`, uma linha para `COBERTURA_INDISPONIVEL`, três marcadores `<!-- gerado de -->`, uma linha de glossário e a reescrita da regra de um gate. Nada disso exige rediscutir arquitetura. **O nono exige.**

O ARB3-001 não é edição de texto porque não há redação que faça um nonce de servidor existir sem servidor. É escolha entre três mecanismos, com custos diferentes, e é decisão do fundador por ADR. Congelar sem ela significa entregar à Fase 1 um critério de aceite que ninguém pode atender e à Fase 2 uma escolha entre desfazer o ARB2-004 e produzir falso positivo no cenário do metrô.

**Sequência que eu recomendaria:** decidir o mecanismo de nonce por ADR (horas de decisão, não semanas), aplicar as oito correções de texto, e **não** rodar uma quarta rodada completa. A quarta verificação deve ser restrita ao ARB3-001 e à conferência no diff dos outros oito, mais a verificação 4 que ficou pendente. A Fase 0 pode começar hoje: nada nesta lista a bloqueia, e três dos itens pendentes dependem justamente de dados que só ela produz.
