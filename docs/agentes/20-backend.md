# Documento 4 — Módulo 20: Backend

**Versão:** 2.2 | **Substitui:** módulo 20 v1.0, v2.0 e v2.1 (correções ARB2 e ARB3)
Aplica-se a todo código em `backend/` e ao painel quando consome a API. Pressupõe `00-nucleo.md`. Arquitetura de referência: Documento 2, §18 a §27.

Este módulo **não é fonte** de conteúdo arquitetural (núcleo §0): onde reenuncia os Documentos 2 ou 3, a fonte prevalece e a divergência é bug.

---

## 1. Estrutura de módulos NestJS

Módulos conforme Documento 2, §18. A lista é reenunciada por conveniência e **gerada e validada em CI** contra a origem (núcleo §11):

<!-- gerado de Documento 2, §18 — validado em CI; não editar aqui -->
`identity`, `users`, `devices`, `street-mode`, `events`, `emergency`, `watchdog`, `trusted-contacts`, `notifications`, `locations`, `subscriptions`, `billing`, `audit`, `support`, `privacy`, `administration`.

- Cada módulo expõe serviços por interface; outro módulo consome a interface, nunca classes internas, repositórios ou tabelas de outro módulo. O monólito é modular para permitir extração futura; acesso cruzado a tabela mata essa opção.
- Comunicação entre módulos com efeito colateral: evento de domínio via outbox (§4), não chamada síncrona encadeada, quando a consistência permitir.
- Controller: valida entrada, autentica, autoriza, delega. Zero regra de negócio em controller.
- Regra de negócio em serviços de domínio testáveis sem HTTP e sem banco real, com repositórios atrás de interface.
- DTO de entrada e saída ≠ entidade de persistência ≠ modelo de domínio. Mapear explicitamente.
- SQL explícito nas consultas críticas (ingestão de eventos, **seleção de prazos do vigilante**, localização, autorização); ORM para CRUD trivial. Query crítica nova exige `EXPLAIN` comentado na PR se tocar tabela grande.
- **O módulo `privacy` tem fase dona:** Fase 2 (Documento 3, §30.5). Ele atende exclusão, exportação, acesso, correção e portabilidade, e escreve em `privacy_requests`. Antes desta correção a tabela existia sem código que a escrevesse.

---

## 2. API

- REST JSON versionada em `/api/v1`; OpenAPI é o contrato: mudança de endpoint sem atualizar OpenAPI é PR reprovada.
- Compatibilidade: dentro de `v1`, mudanças só aditivas. Remoção ou mudança de semântica exige `v2` do recurso e ADR.
- Validação de schema em **toda** entrada (body, query, params, headers relevantes) com whitelist de campos; mass assignment proibido por construção.
- **Idempotência: todo endpoint mutante tem linha na tabela do Documento 2, §16.3.** A tabela cobre check-in, heartbeat, acionamento manual, confirmação e encerramento de protocolo, ciência do contato, simulação, convite, concessão, revogação, token de push, transferência de aparelho e billing. **Endpoint mutante novo sem linha na tabela falha o build** (núcleo §11) — a proibição do núcleo §6 é absoluta e por isso a tabela não pode ser parcial. Armazenar `(chave, usuário, hash do corpo, resposta)`; repetição idêntica devolve a resposta original; **mesma chave com corpo diferente devolve `409`**.
- Caso mais sensível: `POST /emergencies`. A chave de idempotência é gerada pelo cliente **antes** do primeiro envio e reusada em todo retry. Sem isso, dois envios de alguém em pânico com rede ruim viram dois protocolos e dois SMS.
- Erros no envelope padrão (Documento 2, §21.4): `code` estável em SCREAMING_SNAKE, `message` em pt-BR segura para exibição, `correlationId`. Proibido stack trace, SQL, path interno ou dado sensível em erro. Códigos novos obrigatórios: `EVENT_TOO_OLD` (§3), `TIMING_PARAM_OUT_OF_POLICY` (§7), `CONFIRMATION_SIGNATURE_INVALID` (§6).
- Paginação por cursor; limites de payload; timeouts explícitos em toda chamada externa; retry apenas em operação idempotente, com backoff e jitter.
- Rate limiting por usuário e por IP em: auth, recuperação, convite, **simulação de protocolo** e ingestão.
- `correlation_id` aceito ou gerado em middleware e propagado a logs, filas, traces e ao vigilante.
- **Não existe canal de comando do servidor para o aparelho.** `GET /events/sync` foi removido (Documento 2, §37.2). Criar endpoint com essa função exige ADR.

---

## 3. Eventos: ingestão, idempotência e lacunas

Referência: Documento 2, §16.

- Envelope do evento conforme §16.2: `event_id` (UUIDv7 do aparelho), `installation_id`, `session_id?`, `sequence`, `event_type`, `schema_version`, `occurred_at_utc`, `occurred_at_elapsed_ms`, `boot_id`, `payload` e, quando o evento altera prazo ou estado de proteção, `signature` (§6).
- **Deduplicação em `event_dedup(installation_id, event_id, first_seen_at)`**, tabela pequena e não particionada. Proibido criar índice único global nas tabelas quentes (§5). Reenvio retorna o mesmo ACK sem repetir efeitos. Teste obrigatório: enviar o mesmo lote duas vezes e provar efeito único.
- **Idade máxima de evento** (Documento 2, §16.7): medida por `server_received_at`, valor provisório de 30 dias, dono ADR-0009. Evento mais antigo é rejeitado com `EVENT_TOO_OLD`, registra lacuna e emite métrica. `event_dedup` retém pelo mesmo prazo — o que a mantém pequena. A regra "evento crítico não expira" passa a valer **dentro** da idade máxima; sem isso, a retenção da tabela de deduplicação era infinita por definição.
- Ordenação: o servidor **nunca** decide por `occurred_at` do aparelho. Usa `sequence`, horário do servidor e regras de reconciliação do domínio.
- **Cursor por instalação:** `last_contiguous_sequence`, `highest_sequence`, `gaps`. Lacuna aberta além do limiar gera evento `SECURITY` e métrica; o painel exibe "período sem dados" em vez de omitir. Teste obrigatório: enviar 1, 2, 4, 5 e provar a detecção; entregar o 3 atrasado e provar a reconciliação.
- Eventos fora de ordem e duplicados são caso normal, com teste dedicado, não edge case.
- Conflito envolvendo emergência: **nunca** last-write-wins. Precedência do Documento 2, §10.3: confirmação ou encerramento autenticado do titular > ação de operador registrada > automação do vigilante > inferência do aparelho. Empate irresoluto escala para o estado mais seguro e reversível.
- `schema_version` evolui de forma aditiva; consumidor tolera versão anterior. Quebra exige ADR e migração de consumidores.

---

## 4. Transactional outbox e filas

- Toda operação que persiste mudança e precisa notificar grava o evento na `event_outbox` **na mesma transação** do domínio. Publisher separado lê a outbox e publica. Proibido publicar direto na fila dentro do fluxo da request.

```typescript
// CORRETO: domínio + outbox na mesma transação
await this.tx(async (trx) => {
  await this.protocols.escalate(trx, protocolId, level);
  await this.outbox.enqueue(trx, {
    type: 'emergency.escalated',
    aggregateId: protocolId,
    testMode: protocol.testMode,   // obrigatório: viaja com o envelope
    payload: sanitized(payload),
  });
});

// INCORRETO: pode confirmar a operação e perder a notificação
await this.protocols.escalate(protocolId, level);
await this.queue.publish('critical-alerts', payload);
```

- **Especificação do publisher** (Documento 2, §20.1): `SELECT ... FOR UPDATE SKIP LOCKED`, lote pequeno, ordenação por `aggregate_id`, marcação idempotente, métrica `outbox_lag` com alerta ao exceder o SLO de alerta. `LISTEN/NOTIFY` é aceleração opcional, não obrigação.
- **`modo_teste` é campo obrigatório do envelope** do outbox, das filas, da DLQ, de `notification_deliveries` e da auditoria. Nenhum caminho de notificação lê o payload sem consultar a flag; verificação no CI. Sem isso, uma DLQ reprocessada manualmente reenvia simulação como alerta real, ou o contrário.
- Consumidores idempotentes (tabela inbox ou chave natural). Reprocessamento não pode duplicar alerta de emergência nem SMS.
- Filas e prioridades conforme Documento 2, §20.2. **Job de emergência nunca compartilha worker com fila genérica.**
- DLQ obrigatória com alerta, motivo e payload **sanitizado** — nunca coordenadas, tokens ou telefone completo. Reprocessamento manual auditado.
- Retry com backoff exponencial e jitter, com limite; evento crítico não expira por política de retry genérica, dentro da idade máxima do §3.

---

## 5. Banco de dados e migrations

- PostgreSQL. Toda mudança de schema por migration versionada no repositório; proibida alteração manual em qualquer ambiente.
- **Expand-contract obrigatório** para mudança incompatível: adicionar o novo e fazer deploy; migrar e fazer backfill; mudar a leitura; remover o antigo em migration posterior, no mínimo um release depois. Proibido `DROP` ou `ALTER` destrutivo no mesmo deploy que o código que dele depende.
- Toda migration declara estratégia de rollback. Migration em tabela grande avalia lock e usa abordagem online (`CONCURRENTLY` etc.).
- Migrations rodam em banco efêmero no CI e, para mudanças críticas, em cópia de staging antes de produção.
- **Tabelas quentes prontas para particionamento** (Documento 2, §19.5): `security_events`, `location_samples`, `notification_deliveries` e `audit_logs` nascem com `server_received_at` e **sem índice único que impeça particionamento futuro**. A deduplicação vive em `event_dedup`. Ativação do particionamento por ADR-0009, cujo prazo é "antes do beta **ou** imediatamente após o teste de carga da Fase 2 se ele indicar necessidade, o que vier primeiro".
- **Tabelas novas exigidas pelas correções ARB2:** `service_outages(started_at, ended_at, scope, source)` para a supressão do §7, e `device_keys` guardando a chave pública de `K_confirmacao` (§6).
- RLS somente com o mecanismo completo do Documento 2, §19.2: papel de aplicação sem `BYPASSRLS`, `SET LOCAL app.current_user_id` por interceptor único e teste de isolamento na mesma conexão. Sem os três, remoção por ADR-0010. **RLS nunca substitui a autorização na aplicação.**
- Colunas sensíveis criptografadas na aplicação (módulo 30, §3); toda tabela de localização carrega `retention_class` e participa do job de retenção. Criar tabela com dado pessoal sem classe de retenção é PR reprovada.
- Constraints no banco (FK, unique, check) refletem invariantes do domínio; "valida só na aplicação" não é aceitável para invariante de segurança.

---

## 6. Autenticação e autorização

Referência: Documento 2, §22; Documento 3, §20.

- **Sessão própria do backend.** O provedor gerenciado atua apenas como verificação de identidade no login. Nenhuma feature importa o SDK do provedor diretamente.
- Access token curto (≤ 15 min) assinado pelo backend, com `installation_id` e `acr`. Refresh opaco em tabela, com família, **rotação a cada uso** e revogação da família inteira ao detectar reuso, gerando evento `SECURITY`.
- Revogação por linha, efetiva no próximo access token, com teste que prova o efeito.
- Autorização em duas camadas: RBAC (usuário, contato, suporte, admin, serviço) e verificação por recurso em **todo** endpoint que recebe id. IDOR é a vulnerabilidade mais provável do produto.
- **Todo endpoint com identificador de recurso nasce com teste de autorização negativa** (ator errado recebe 403 ou 404) no mesmo PR. Sem esse teste, o endpoint não existe. A verificação é automatizada (núcleo §11).
- **Verificação de assinatura de confirmação** (Documento 2, §16.8), com **dois regimes**, porque os eventos têm premissas de conectividade opostas:
  - **confirmação de check-in:** assinatura sobre material do aparelho — `(installation_id, session_id, check_in_id, sequence, expected_next_checkin_at, boot_id, desafio_de_sessao?)`. **Nunca exigir nonce aqui:** a confirmação tem de ser produzível sem rede (núcleo §2.2), e exigir valor emitido pelo servidor tornaria o caso do metrô um falso positivo garantido. Replay é barrado por `event_dedup` mais a linha de idempotência por `check_in_id`; `sequence` precisa avançar;
  - **encerramento de sessão, encerramento de protocolo e alteração de parâmetro de temporização:** nonce fresco de uso único, obtido na mesma requisição. São online por natureza.
  - **Desafio de sessão** (§16.8.2): toda resposta de registro e de confirmação devolve um desafio de uso único para a confirmação seguinte, junto do `policy_version` vigente. O servidor aceita desafio conhecido mas não corrente enquanto `sequence` avança e o `check_in_id` está sem uso; rejeita desconhecido; e **aceita ausência de desafio** quando a instalação nunca registrou sessão.
  - Assinatura inválida devolve `CONFIRMATION_SIGNATURE_INVALID` e **não** produz transição. `confirmation_type` é **derivado** da verificação, nunca aceito do cliente — antes desta correção era campo autodeclarado, e quem tivesse o token confirmava check-ins sem nunca ver um prompt biométrico. Limitação a declarar no suporte e na documentação: a assinatura prova autenticação validada pelo sistema, **não** legitimidade da pessoa; sob coação, a assinatura é válida (Documento 3, §13.5).
- Step-up nas ações do Documento 3, §20.3:

<!-- gerado de Documento 3, §20.3 — validado em CI; não editar aqui -->
> consultar localização; alterar contato; encerrar protocolo; exportar dados; excluir conta; mudar e-mail; transferir aparelho; **alterar intervalo de check-in, `grace_seconds` ou qualquer parâmetro de temporização da sessão**.
- Concessões de contato de confiança: escopo mínimo, expiração, ativas somente durante protocolo autorizado, revogáveis, auditadas. **Nenhum caminho de código dá a contato acesso a histórico completo, configuração da conta ou capacidade de causar transição de estado de protocolo.**
- Convite pré-aceite: finalidade declarada, aviso de transparência na primeira mensagem, retenção curta com descarte automático, rate limit por janela, respeito à recusa e bloqueio de reenvio.
- **Troca de aparelho** revoga credenciais e a instalação anterior, e **não encerra sessão ativa nem protocolo aberto** (Documento 2, §4.7). A instalação nova não herda a sessão; o prazo registrado permanece armado. Teste obrigatório, espelhando o de billing: transferência com sessão ativa e com protocolo aberto.

---

## 7. Vigilante de prazo

Referência canônica: Documento 2, §18.7. Este é o componente que dá existência à garantia externa. Este módulo **não** o redefine: as regras abaixo são operacionais e, onde reenunciam, a fonte prevalece.

- Registro de sessão idempotente por `session_id`, com `expected_next_checkin_at`, `grace_seconds` e `policy_version`.
- Cada confirmação carrega o próximo `expected_next_checkin_at`. O aparelho é o agendador da intenção; **o servidor é o executor do prazo — executor com limite.**
- `policy_version` conforme Documento 2, §18.7.2: é o servidor que serve os limites, a sessão persiste a versão com que foi armada, e alteração de limite nunca encurta prazo de sessão em curso. O endpoint de temporização é `PATCH /street-mode/sessions/{id}/timing` e devolve o `policy_version` vigente.
- **Limite obrigatório sobre os dois parâmetros vindos do aparelho** (§18.7, item 2a): `expected_next_checkin_at` **e** `grace_seconds` são limitados pelo `policy_version` do servidor; valor além do máximo devolve `TIMING_PARAM_OUT_OF_POLICY` e o prazo anterior permanece armado. Aumento vale só a partir da confirmação seguinte; redução vale de imediato. Alteração exige step-up e, em sessão ativa, gera evento `SECURITY` e aviso por canal externo. Sem esse limite, quem está com o aparelho desbloqueado desarma o vigilante mudando uma configuração, sem autenticar nada.
- Avaliação agendada em `expected_next_checkin_at + grace_seconds + margem_de_rede`. O valor de `margem_de_rede` está **`[ABERTO — FASE 0]`**: mínimo derivado do p99 de atraso de disparo medido por fabricante, e **re-medido com o código de produção na Fase 4**.
- **Mecanismo de execução:** fila com entrega retardada por `session_id`, mais varredura periódica de `check_in_schedules` como rede de segurança. Os dois, porque a varredura sozinha não sustenta o p99 sob carga sazonal e a fila sozinha perde prazos em falha de broker. `EXPLAIN` da seleção de prazos registrado. Teste de carga próprio na Fase 4.
- Ao disparar sem confirmação: abre `SUSPEITA` sem notificar ninguém e aguarda a `janela_de_reconciliacao` por eventos atrasados. Sem confirmação ao fim da janela: `ALERTANDO`.
- **Idempotente:** reexecução do job não duplica protocolo, transição, alerta nem SMS. A varredura é idempotente contra a fila por esta regra.
- Heartbeat leve distingue "app vivo e usuário silencioso" de "aparelho inalcançável". As duas condições escalam, com texto diferente ao contato. Cadência é parâmetro do ADR-0005, valor provisório de 15 minutos, e alimenta o `silencio_maximo_de_comunicacao` da cobertura (Documento 2, §11.2). Ausência de heartbeat **fora** de sessão ativa não gera protocolo.
- **Comportamento terminal:** esgotados os tetos de tentativa por canal e de duração de `ALERTANDO`, o protocolo vai para `SEM_CIENCIA` (Documento 2, §10.2, linha 14) — informa o titular, permanece aberto e reversível, não afirma nada sobre ele. `EMERGENCIA` só amplia compartilhamento se houver ao menos uma ciência registrada.
- **Guarda de anomalia com dois critérios** (§18.7.1): limite **absoluto** (teto de acionamentos externos por janela e teto de percentual das sessões protegidas ativas) e linha de base **relativa** segmentada, com mínimo de observações por célula — abaixo do mínimo vale só o absoluto. Motivo: com 20 a 100 usuários no beta, a segmentação por região × operadora × versão × fabricante produz células vazias e o critério relativo é indefinido justamente onde uma queda de operadora atinge quase toda a base. Ao exceder qualquer limiar, novos protocolos entram em `RETIDO` — titular e painel seguem informados, contato não é acionado, e um humano libera ou classifica, com registro.
- **Simulações ficam fora do numerador, da linha de base e do denominador.** Sessão de simulação não é sessão protegida. Métrica obrigatória nova: sessões protegidas ativas por janela, que é o denominador.
- **Indisponibilidade conhecida do próprio backend suprime a abertura e reagenda**, com mecanismo: janelas em `service_outages` alimentadas por monitor **externo** à infraestrutura que falhou; prazo vencido dentro de uma janela não abre protocolo e é reagendado para `agora + margem_de_rede + jitter`; teto de taxa na recuperação, independente da guarda; um evento de auditoria por prazo suprimido, com o id da janela. Sem espalhamento, a recuperação **é** o evento de alerta em massa; sem auditoria, supressão indevida de alerta verdadeiro (SEV-1) é indetectável.
- Nenhuma ação destrutiva, em nenhuma circunstância.

---

## 8. Billing

Referência: Documento 2, §27.

- Fonte de verdade do entitlement é o backend, após validação server-side do purchase token. O app nunca decide entitlement sozinho.
- Webhooks idempotentes por id de notificação; processamento fora de ordem tolerado; reconciliação periódica com a Play.
- Estados de assinatura exatamente os do Documento 2, §27; transição gera evento auditável.
- **Sessão ativa e protocolo aberto não são interrompidos por estado de cobrança.** A restrição incide apenas na ativação de nova sessão. Encerrar protocolo, consultar histórico próprio, exportar e excluir conta permanecem disponíveis sem assinatura. Teste obrigatório: expiração de entitlement com sessão ativa e com protocolo aberto.
- Nenhum dado completo de cartão em nenhum sistema próprio.

---

## 9. Observabilidade

- OpenTelemetry em API, workers e vigilante; todo fluxo carrega `correlation_id`, `trace_id`, `event_id`, `device_ref` pseudonimizado e `protocol_id`.
- Métricas mínimas por feature nova: latência, taxa de erro e as métricas de produto do fluxo. Feature crítica sem métrica é incompleta.
- Métricas obrigatórias do núcleo do produto: `outbox_lag`, **atraso de execução do vigilante, com SLO p95 ≤ 30 s e p99 ≤ 60 s (provisórios, ADR-0005)**, lacunas detectadas, **tempo até a primeira ciência do contato — registrando explicitamente o caso sem ciência**, **sessões protegidas ativas por janela**, **acionamentos da guarda de anomalia**, **prazos suprimidos por indisponibilidade própria**, falha por canal de notificação, **entrega de SMS por operadora**.
- Logs estruturados em JSON, política do módulo 30, §2. `DEBUG` desligado em produção por configuração de build, não por disciplina.
- Alertas para: DLQ maior que zero em fila crítica; atraso de alerta acima do SLO; `outbox_lag` acima do limiar; taxa de erro; falha de webhook de billing; falha do job de retenção; falha do provedor de SMS; guarda de anomalia acionada; **janela de indisponibilidade aberta há mais de X minutos com prazos acumulados**.

---

## 10. Checklist backend para PR

- [ ] Nenhum acesso cruzado a tabela ou classe interna de outro módulo
- [ ] OpenAPI atualizado; mudança compatível ou ADR
- [ ] Validação de schema com whitelist em toda entrada nova
- [ ] **Endpoint mutante novo tem linha na tabela do Documento 2, §16.3**; consumidor de fila idempotente
- [ ] Deduplicação em `event_dedup`; nenhum índice único global em tabela quente; idade máxima de evento aplicada
- [ ] Lacuna de sequência detectada e sinalizada
- [ ] Efeitos colaterais via outbox na mesma transação, **com `modo_teste` no envelope**
- [ ] **Parâmetro de temporização vindo do aparelho é limitado por `policy_version`**
- [ ] **Assinatura de confirmação verificada; `confirmation_type` derivado, não declarado**
- [ ] Vigilante idempotente; nenhuma ação destrutiva; supressão por indisponibilidade auditada
- [ ] Migration com rollback e expand-contract se incompatível
- [ ] Autorização por recurso e teste negativo para todo endpoint com id
- [ ] Nenhum dado sensível em log, erro, SMS ou payload de DLQ
- [ ] Métricas e correlação nos fluxos novos
- [ ] Timeouts e retry corretos em chamadas externas
