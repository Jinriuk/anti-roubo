# Changelog — correções da segunda rodada (ARB2)

**Data:** 26/07/2026. **Escopo:** 33 achados + MOD-01, MOD-02 + ADD-01 a ADD-07 + correção de data.
**Versões resultantes:** Doc 1 v2.1 · Doc 2 v2.1 · Doc 3 v2.1 · Doc 5 v2.2 · Doc 4 README v2.1 · núcleo v2.2 · módulos 10 a 50 v2.1.
**Regras respeitadas:** nenhum item `[ABERTO — FASE 0]` fechado; nenhuma seção do Doc 2 renumerada (conteúdo novo entrou como §10.4, §11.2, §12.5, §16.7, §16.8); nenhuma reescrita fora dos achados apontados.

---

## Achados

| ID | Arquivos e seções | O que mudou |
|---|---|---|
| **ARB2-001** | Doc 2 §14.3; Doc 3 §27.4, §28.2, §13.3; mód. 10 §8; mód. 30 §3, §4; Doc 1 §8.1, §15.1, §21.1; Doc 5 Fase 1 | Hierarquia reescrita com três chaves. `K_leitura` e `K_confirmacao` no Keystore com `BIOMETRIC_STRONG \| DEVICE_CREDENTIAL`; o fallback é o credencial do aparelho, não o PIN do aplicativo. Removido o mecanismo inexistente ("PIN autoriza `K_leitura`") e o contador circular. Exigência de tela de bloqueio segura e destino do PIN ficaram **pendentes de decisão** |
| **ARB2-002** | Doc 2 §14.3, §14.4, §15; Doc 3 §26.5; mód. 10 §7, §8; mód. 30 §3, §5; Doc 5 Fase 1 | Amostra de localização pendente passa para `K_dados`, legível por worker. Chave declarada por coluna no modelo local. Limitação da janela pendente-até-ACK declarada; job de purga reclassificado como controle de segurança |
| **ARB2-003** | Doc 2 §18.7 (item 2a), §22.3; Doc 3 §13.2, §13.3, §20.3; mód. 20 §7; mód. 30 §4 | Limite de servidor por `policy_version` sobre os parâmetros vindos do aparelho; aumento só vale da confirmação seguinte; alteração é step-up com `SECURITY` e aviso externo. Ameaça "empurrar o prazo" acrescentada ao §13.2 |
| **ARB2-004** | Doc 2 §16.8 (nova), §18.2, §36; Doc 3 §7.2, §13.5; mód. 20 §6; mód. 30 §4 | Confirmação assinada por `K_confirmacao`, verificada no servidor; `confirmation_type` derivado, não declarado. `device_keys` ganhou propósito. Substituída a redação "assinatura quando justificada" |
| **ARB2-005** | Doc 2 §11.1; Doc 3 §51 | Texto próprio para `COBERTURA_SUSPENSA`; distinção "o servidor sabe da sessão?" explicitada; bloqueador de release ampliado para texto que não corresponde ao estado |
| **ARB2-006** | Doc 2 §16.3; mód. 20 §2, §10; núcleo §6, §11; Doc 5 Fase 2 | Tabela de idempotência completada com 11 casos ausentes, inclusive acionamento manual com chave gerada antes do primeiro envio. Gate de CI para endpoint mutante sem linha |
| **ARB2-007** | núcleo §0; Doc 2 §34.5; Doc 5 Fase 0; núcleo §11; README | Tabela dos quatro itens abertos com o local exato do marcador; marcadores inseridos no Doc 2 §34.5 e no Doc 5; gate de CI conferindo ocorrências contra a tabela; regra de valor provisório declarado |
| **ARB2-008** | Doc 2 §12.2, §35; mód. 10 §6 | Tabela de candidatos com tipo de FGS, timeout de 6 h/24 h, viabilidade via `BOOT_COMPLETED`, exigência de loja e efeito na cota de jobs do Android 16. `location` identificado como não proibido no boot; `dataSync` descartado para sessão longa |
| **ARB2-009** | Doc 2 §34.5, §35; mód. 10 §11; mód. 40 §5; Doc 5 Fase 0, Fase 7 | Linha de base passa a 15/16/17. Linha própria para a declaração de escopo mínimo de `FINE`, **reclassificada como gate da Fase 7** conforme §4 da instrução. Matriz atualizada |
| **ARB2-010** | Doc 2 §12.2, §34.6; mód. 30 §6; mód. 50 §8 | Acoplamento entre ADR-0007 e a política de monitoramento declarado; parecer antecipado para antes do ADR; decisão sobre `isMonitoringTool` e divulgação na loja acrescentadas aos entregáveis |
| **ARB2-011** | Doc 2 §18.7.1; Doc 3 §25.4; mód. 20 §7; mód. 40 §3, §4; Doc 5 Fase 4 | Guarda com dois critérios: limite absoluto (funciona com n=20) e linha de base com mínimo de observações. Métrica nova de sessões protegidas ativas |
| **ARB2-012** | Doc 2 §18.7.1, §19.1; Doc 3 §21.1, §25.4, §36.4; mód. 20 §5, §7, §9; mód. 40 §4 | Mecanismo de supressão: `service_outages` alimentada por monitor externo, reagendamento com jitter, teto de taxa na recuperação, auditoria por prazo suprimido, runbook |
| **ARB2-013** | Doc 2 §12.5 (nova), §29; Doc 1 §8.2; mód. 20 §9; mód. 40 §4; Doc 5 Fase 4 | Orçamento de latência ponta a ponta com oito termos e teto no ADR-0005; SLO de atraso do vigilante; medição fim a fim na Fase 4 |
| **ARB2-014** | Doc 2 §18.7 (item 8), §33; mód. 20 §1, §7; mód. 40 §4; Doc 5 Fase 4 | Mecanismo de execução declarado (fila retardada + varredura); teste de carga do vigilante em dois cenários; `EXPLAIN` da seleção de prazos |
| **ARB2-015** | Doc 2 §4.7; Doc 3 §35.2 (por remissão); mód. 20 §6; mód. 40 §4; Doc 5 Fase 2 | Troca de aparelho não encerra sessão ativa nem protocolo aberto; teste obrigatório espelhando o de billing |
| **ARB2-016** | Doc 2 §4.7, §14.4; mód. 10 §8; mód. 40 §5, §7; núcleo §11; Doc 5 Fase 1 | Exclusão de backup nos dois formatos, com `<device-transfer>` explícito; lint de manifesto; testes por nuvem no `minSdk` e por transferência D2D |
| **ARB2-017** | Doc 2 §11.2 (nova); mód. 10 §3; mód. 40 §2, §3; Doc 5 Fase 1 | Tabela de transições de cobertura, parâmetro de silêncio máximo, matriz de combinação com a máquina da sessão, obrigação de teste |
| **ARB2-018** | Doc 5 Fase 0 (escopo, medições, aceite, interrupção); Doc 1 §20.2, §24.2, §30; Doc 2 §25.1 | Viabilidade de canal movida para a Fase 0: cotações, entrega por operadora, custo por cadastro/alerta/falso positivo, ADR-0011 provisório |
| **ARB2-019** | mód. 30 §2; núcleo §11; mód. 50 §9 | Lista de auditoria deixou de ser cópia manual: virou bloco **gerado** e validado em CI. Os quatro itens omitidos voltaram, incluindo rotação de chave e alteração administrativa |
| **ARB2-020** | Doc 3 §30.5, §30.7; Doc 1 §21.1, §21.2; mód. 20 §1; Doc 5 Fase 2 | Direitos do titular e exportação alocados à Fase 2; encarregado como entregável nomeado da Fase 5; `recovery_guide` promovido a núcleo na Fase 4 |
| **ARB2-021** | ver MOD-01 | — |
| **ARB2-022** | Doc 2 §16.7 (nova), §17; mód. 20 §3; mód. 40 §2 | Idade máxima de evento (30 dias provisórios), `EVENT_TOO_OLD`, retenção de `event_dedup` amarrada a ela |
| **ARB2-023** | Doc 2 §12.4 (item 5); mód. 10 §9; mód. 30 §4; Doc 5 Fase 1 | Ação de notificação abre e nunca conclui confirmação forte; assimetria com o acionamento manual declarada como deliberada |
| **ARB2-024** | Doc 2 §12.4 (item 6); Doc 3 §19.1, §19.3; mód. 10 §6, §9; Doc 1 §26.1 | Regra simétrica para perda de capacidade de disparo local, escrita antes do ADR-0007 |
| **ARB2-025** | Doc 2 §10.2 (linha 14), §25.2; Doc 3 §43.4; mód. 20 §7; Doc 5 Fase 3 | Estado `SEM_CIENCIA`, tetos de tentativa e duração, `EMERGENCIA` condicionado a alguma ciência. Número de contatos ficou **pendente de decisão** |
| **ARB2-026** | Doc 2 §18.7 (item 6), §11.2; mód. 20 §7; mód. 40 §6; Doc 5 Fase 0 | Cadência de heartbeat como parâmetro do ADR-0005, provisória em 15 min, com medição de consumo e de latência de detecção |
| **ARB2-027** | Doc 5 Fase 0 e Fase 4; mód. 40 §5; mód. 50 §1 | Isenção limitada ao código; método de medição revisado antes da coleta; re-medição do p99 com código de produção na Fase 4 |
| **ARB2-028** | núcleo §3.1, §11; mód. 50 §3, §4; mód. 40 §5, §7 | Rótulo de evidência derivado dos caminhos alterados; humano só remove com justificativa registrada |
| **ARB2-029** | ver MOD-02 | — |
| **ARB2-030** | Doc 2 §35, §39; mód. 10 §9, §11; mód. 40 §5; Doc 5 Fase 0 | Custo da faixa 30→36 listado ponto a ponto; ADR-0002 com dois critérios; dois aparelhos abaixo de API 33 na matriz; pré-condição de sessão para API < 33 |
| **ARB2-031** | Doc 2 §39, §19.5; mód. 20 §5; Doc 5 Fase 2 | Prazo do ADR-0009 alinhado ao gatilho do teste de carga; decisão explícita como critério de aceite da Fase 2 |
| **ARB2-032** | Doc 2 §11.1; Doc 5 Fase 1 | Estado `COBERTURA_INDISPONIVEL` com texto próprio para build sem backend; critério da Fase 1 reescrito |
| **ARB2-033** | Doc 2 §10.2, §18.7.1, §20.1; Doc 3 §25.5; mód. 20 §4, §7; mód. 30 §6; Doc 5 Fase 4 | `modo_teste` obrigatório em outbox, filas, DLQ, entregas e auditoria, com gate de CI; simulação fora do numerador, da linha de base e do denominador |

## Aceitos com modificação

| ID | Arquivos e seções | O que mudou |
|---|---|---|
| **MOD-01** | núcleo §0; mód. 20 (cabeçalho, §1, §7); mód. 30 (cabeçalho, §2); mód. 10 §5; mód. 50 §4, §9; README | Hierarquia numerada **preservada**, como instruído. Acrescentada a cláusula única: o Doc 4 não é fonte de conteúdo arquitetural, de estados, de contratos, de modelo de dados ou de ameaças; reenunciação divergente é bug. Reenunciações úteis viraram blocos `<!-- gerado de ... -->` validados em CI |
| **MOD-02** | Doc 2 §34.5; Doc 1 §10.1, §24.1; mód. 10 §7; mód. 30 §9 | Separação em duas linhas: o que o **produto promove** (conjunto de recursos centrais — a cerca não fica barrada) e o que o **formulário declara** (recurso principal, `[ABERTO — FASE 0]`). Não foi tratado como regra de exclusão de recursos |

## Adições incorporadas

| ID | Arquivos e seções | O que mudou |
|---|---|---|
| **ADD-01** | Doc 2 §14.3; Doc 3 §27.4, §28.2; mód. 10 §8; mód. 30 §3 | `setUserAuthenticationParameters(0, …)` — autenticação a cada uso, timeout zero, com o custo de experiência declarado |
| **ADD-02** | Doc 2 §14.3, §14.4; Doc 3 §26.5; mód. 10 §7; mód. 30 §3 | Amostra apagada no ACK, sem reencriptação; proibido implementar rechaveamento de amostra |
| **ADD-03** | Doc 3 §7.2, §13.5; Doc 2 §16.8; mód. 20 §6; mód. 30 §4; Doc 1 §8.1 | Promessa redigida no escopo exato ("quem passa pela tela de bloqueio"), com a coação declarada como não coberta |
| **ADD-04** | Doc 2 §18.7 (item 2a), §22.3; Doc 3 §13.3, §20.3; mód. 20 §7; mód. 30 §4 | `grace_seconds` recebeu o mesmo tratamento de `expected_next_checkin_at` |
| **ADD-05** | Doc 2 §10.1 (linha 10a), §10.4 (nova); Doc 5 Fase 4; mód. 30 §6 | Três saídas descritas com custo e limitação residual; recomendação técnica registrada (opção A); `timeout` como transição terminal suspenso até a decisão |
| **ADD-06** | Doc 2 §18.7.1; Doc 3 §25.4; mód. 20 §7; mód. 30 §6 | Exclusão de simulações declarada nos três lugares, denominador incluído |
| **ADD-07** | Doc 2 §25.1, §22.2; Doc 3 §36.4, §43.4; mód. 30 §5; mód. 40 §5; Doc 5 Fase 0; Doc 1 §30 | Filtragem de SMS com URL por operadora como teste da Fase 0, com plano B de affordance e runbook. `VERIFICAR:` sobre atraso de mensagem com código no Android 17 registrado em Doc 2 §22.2 |

## Correção de data (§4 da instrução)

| Item | O que mudou |
|---|---|
| Data da política de escopo mínimo | Corrigida: a exigência vincula apps que alvejam Android 17+, e o alvo API 37 só é exigido por volta de ago/2027. Reclassificada como **gate da Fase 7**, com linha própria mantida no §34.5 e rascunho produzido na Fase 0. A data de out/2026 pertence à política de contatos |
| `VERIFICAR:` nº 9 | **Encerrado.** A concessão automática de notificação em tela cheia é restrita, desde 22/01/2025, a apps com funcionalidade de chamada ou despertador entre os que alvejam Android 14+, com declaração no Play Console. A afirmação dos documentos estava correta e o texto do Doc 2 §12.4 passou a citar a data |

---

## O que não foi corrigido, e por quê

1. **Os quatro itens `[ABERTO — FASE 0]` continuam abertos.** Marcadores inseridos, decisões não tomadas. Onde uma regra precisava de número, entrou **valor provisório declarado**, com ADR dono: `margem_de_rede` (p99 da Fase 0), cadência de heartbeat (15 min), silêncio máximo de comunicação (3 heartbeats), idade máxima de evento (30 dias), SLO do vigilante (p95 30 s / p99 60 s), janela de reconciliação (180 s, já existia).
2. **Três decisões pendentes do fundador**, apresentadas com opções e custos, nenhuma implementada: tela de bloqueio segura e destino do PIN interno (Doc 2 §14.3); número de contatos no MVP (Doc 2 §10.2); saída para o encerramento offline (Doc 2 §10.4). Recomendação técnica registrada apenas para a terceira (opção A), porque as outras duas dependem de dado de mercado e de escopo comercial que não tenho.
3. **Quatro `VERIFICAR:` permanecem abertos**, por não haver fonte oficial suficiente: semântica de invalidação por novo cadastro biométrico em chave que aceita `DEVICE_CREDENTIAL`; comportamento observado de revogação de alarme exato pelo sistema; novos tipos de serviço em primeiro plano relatados no Android 17 (`dataProcessing`, `userInitiatedAction` — apenas fontes secundárias); atraso de mensagem com código no Android 17. Os três primeiros são medição da Fase 0.
4. **Decisão de escopo que eu tomei e que o fundador pode reverter:** a alocação de fases do ARB2-020. Coloquei direitos do titular e exportação na Fase 2 (compartilham autenticação, step-up e auditoria com a exclusão, que já estava lá) e promovi `recovery_guide` a núcleo na Fase 4 (é efeito de transição canônica). Nenhuma das duas era escolha óbvia; se a preferência for adiar os direitos do titular para a Fase 5, o único ajuste é mover as linhas correspondentes no Doc 5 e reabrir o critério de aceite da Fase 5.
5. **Não revisei o que não foi apontado.** Não encontrei defeito novo durante a correção que justificasse achado adicional; a única coisa que chamou atenção e não virou correção é que o `policy_version` aparece agora em três regras (limite de parâmetros, tetos de `ALERTANDO`, cadência de heartbeat) sem que exista uma seção que defina o que ele contém e como é versionado. Registro aqui como candidato a achado da próxima rodada, em vez de corrigir em silêncio.
6. **Não sou o revisor desta correção.** A revisão de acompanhamento dos nove itens de bloqueio é feita em outra conversa, com os dois relatórios anexados.


---

# Adendo — correções da terceira rodada (ARB3)

**Data:** 26/07/2026. **Escopo:** os 21 achados do relatório ARB3 (9 de bloqueio + 11 de backlog + 1 meta).
**Versões resultantes:** Doc 2 v2.1.1 (conteúdo) · Doc 3 v2.2 · Doc 5 v2.3 · núcleo v2.3 · módulos 10, 20, 30, 40, 50 v2.2 · README v2.2 · Doc 1 inalterado (v2.1).

## Itens de bloqueio

| ID | Onde | O que mudou |
|---|---|---|
| ARB3-001 | Doc 2 §16.8 (reescrita, com §16.8.1, §16.8.2, §16.8.3), §18.7.2 (nova), §39 (ADR-0013); Doc 3 §7.2, §13.5; mód. 10 §8; mód. 20 §6; mód. 30 §4; mód. 40 §2; Doc 5 Fase 1 | Nonce removido do caminho do check-in. Assinatura sobre material do aparelho (`sequence`, `check_in_id`, `boot_id`), replay por `event_dedup` mais idempotência; desafio de sessão opcional dá frescor quando houve rede; nonce mantido só nos três eventos online. Cinco opções avaliadas e descartadas por escrito na própria seção |
| ARB3-002 | Doc 2 §16.3, §21.2 | Três linhas acrescentadas (`PATCH /installations/{id}`, `DELETE /me`, `PATCH /street-mode/sessions/{id}/timing`) e o endpoint de temporização criado |
| ARB3-003 | núcleo §0, §11 | Gate de marcadores reescrito: exige ao menos uma ocorrência na seção citada, permite remissões em outras, fixa a cadeia literal com travessão e prevê lista de exceções. `margem_de_rede` passou a ter ADR dono único (0005-B) |
| ARB3-004 | Doc 5, Fase 1 (escopo) | `COBERTURA_LOCAL` → `COBERTURA_INDISPONIVEL`, com o motivo escrito |
| ARB3-005 | Doc 2 §10.2 | `SEM_CIENCIA` ganhou saídas: origem nas linhas 9, 10 e 12, mais a linha 15 (`ciencia_do_contato` retoma `ALERTANDO`) |
| ARB3-006 | Doc 2 §11.2 | `COBERTURA_INDISPONIVEL` como linha 7 (constante de build, sem saída, mutuamente exclusiva) e linha na matriz Sessão × Cobertura |
| ARB3-007 | núcleo §11; mód. 20 §6 | Bloco `<!-- gerado de Documento 3, §20.3 -->` criado; a afirmação "validada em CI" sem marcador foi eliminada; o gate passou a derivar as origens dos marcadores presentes, e §51 saiu da lista por ser remissão sem cópia |
| ARB3-008 | Doc 2 §3 | `strong_confirmation` corrigido; três entradas novas (`K_confirmacao`, `SEM_CIENCIA`, `COBERTURA_INDISPONIVEL`) |
| ARB3-009 | Doc 5, Fase 1 | Seção "Pré-condições de entrada" com ADR-0004, 0005-A, 0013 e a decisão da tela de bloqueio, mais a alternativa de escopar a fase para aparelhos com bloqueio |

## Backlog aplicado na mesma rodada

ARB3-010 (lista de estados sem errata) · ARB3-011 (`K_leitura` protege cache do histórico, não a fonte) · ARB3-012 (termo 9 do orçamento, só no caminho de recuperação) · ARB3-013 (default provisório declarado como interino) · ARB3-014 (política renomeada na primeira linha de `FINE`) · ARB3-015 (ADR-0011 provisório na Fase 0) · ARB3-016 (cláusula do §0 alcança o Documento 5) · ARB3-017 (gate `modo_teste` virou invariante de ponto único de despacho) · ARB3-018 (coluna método e caminho em §16.3) · ARB3-019 (§10.4 movido para depois de §10.3) · ARB3-020 (resíduo humano da remoção do rótulo declarado).

## Verificação 4, que a ARB3 não executou

As 18 afirmações de plataforma foram conferidas contra fonte oficial. Duas correções resultaram:

1. **Notificação em tela cheia:** confirmada em fonte oficial (Play Console Help), com a data de 22/01/2025. A redação anterior dizia "afirmação verificada" sem que o corretor a tivesse verificado — a frase foi substituída pela citação da fonte, e a ref. 22 entrou no §43.
2. **Escopo mínimo de `ACCESS_FINE_LOCATION`:** a fonte oficial prevê fiscalização a partir do fim de outubro de 2026 **para apps que alvejam Android 17+**. A conclusão de tratar o item como gate da Fase 7 está correta; a justificativa anterior ("a data pertence à política de contatos") não estava, e foi reescrita.

Duas afirmações continuam sem fonte oficial citada e estão marcadas como tal: a data exata da estabilização do Android 17 (fontes secundárias; a existência da versão está confirmada em fonte oficial) e a semântica de `setUserAuthenticationParameters` com tempo zero, que ganhou `VERIFICAR:` explícito no §16.8.3 porque a garantia contra pré-assinatura repousa nela.

## Não corrigido

- **ARB3-021** (discordância da regra de escopo) não é achado de documento e não gera edição.
- Os quatro `[ABERTO — FASE 0]` seguem abertos; os `VERIFICAR:` seguem cinco, agora incluindo o de `setUserAuthenticationParameters`.
- Das três decisões pendentes do fundador, **nenhuma foi tomada**. A do encerramento offline ganhou default provisório declarado como interino; a da tela de bloqueio virou pré-condição de entrada da Fase 1, com alternativa de escopo; a do número de contatos segue como estava.
