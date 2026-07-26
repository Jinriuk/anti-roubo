# Documento 4 — Módulo 30: Segurança aplicada ao código

**Versão:** 2.2 | **Substitui:** módulo 30 v1.0, v2.0 e v2.1 (correções ARB2 e ARB3)
Tradução operacional do Documento 3 para quem escreve código. O Documento 3 continua sendo a autoridade; em dúvida de intenção, ele decide. Carregar sempre que a tarefa tocar autenticação, localização, contatos, criptografia, logs, notificações ou dados pessoais.

Este módulo **não é fonte** de conteúdo de segurança (núcleo §0). Listas canônicas não são copiadas à mão aqui: são remetidas, ou geradas e validadas em CI contra a origem. A versão 2.0 mantinha uma cópia abreviada da lista de eventos auditáveis, com quatro itens omitidos — exatamente o anti-padrão que os Documentos 2 §41 e 3 §52 proíbem.

---

## 1. Segredos

- Nenhum segredo (chave, token, senha, connection string, keystore de assinatura) em código, Git, imagem Docker, artefato de build, variável hardcoded, comentário ou log. Sem exceção "temporária".
- Segredos vivem no secret manager; um conjunto por ambiente; acesso por identidade de workload com privilégio mínimo.
- `.env` de exemplo no repositório contém apenas nomes de variáveis com valores falsos óbvios (`CHANGE_ME`).
- Secrets scanning no CI; achado de segredo deixa o build vermelho.
- **Se um segredo vazar** (commit acidental incluído): não basta remover o commit. Rotacionar imediatamente, registrar incidente (Documento 3, §36) e verificar uso indevido. O agente que detectar segredo em qualquer lugar do repositório reporta na hora, mesmo fora do escopo da tarefa.
- **Agentes não recebem credencial de produção** em nenhuma circunstância (§9).

---

## 2. Política de logs

Válida para app, backend, painel, workers, vigilante e ferramentas.

**Permitido:** timestamp, código de evento, versão, fabricante e modelo, status, `correlation_id`, ids pseudonimizados, erro sanitizado, latência.

**Proibido em qualquer nível de log, analytics, crash report, DLQ, mensagem de erro, URL, push ou SMS:**

- localização precisa (coordenadas, endereço) em texto;
- PIN, senha, token, chave, código de recuperação;
- payload sensível completo;
- conteúdo de mensagem ou notificação a contato;
- telefone ou e-mail completo de contato;
- documento pessoal.

Regras:

- Toda serialização para log passa pela biblioteca central de redaction. Logar objeto cru (`toString()` de entidade, body de request) é proibido em fluxo que possa conter dado sensível.
- Modelos com campo sensível implementam `toString()` seguro (em Kotlin, sobrescrever em data class ou usar wrapper `Redacted<T>`).
- Níveis: DEBUG (só dev), INFO, WARN, ERROR, SECURITY, AUDIT. DEBUG desligado em produção por configuração de build.
- **Eventos AUDIT: a lista canônica é o Documento 3, §34.1.** Este módulo não mantém cópia editável. A cópia abaixo é **gerada** e validada automaticamente contra a origem (núcleo §11); se estiver divergente, o build falha e a origem prevalece.

<!-- gerado de Documento 3, §34.1 — validado em CI; não editar aqui -->
> login; logout; MFA; recuperação; contato incluído e removido; localização consultada; protocolo aberto, escalado, retido, liberado e encerrado; ciência de contato; exportação; exclusão; acesso de suporte; mudança de permissão; **rotação de chave**; **alteração administrativa**; liberação manual de protocolo retido, com operador e motivo.

Acrescentam-se, por força das correções ARB2 e já constantes da origem ou dela derivados: alteração de parâmetro de temporização da sessão; prazo suprimido por indisponibilidade própria, com o id da janela.

- Registro append-only com actor, action, resource, timestamp, resultado, origem e `correlation_id`.

---

## 3. Criptografia

**Permitido (lista fechada, verificada por lint — núcleo §11):**

| Uso | Primitiva |
|---|---|
| Cifra simétrica | AES-256-GCM; ChaCha20-Poly1305 quando adequado |
| Hash e integridade | SHA-256; HMAC-SHA-256 |
| Derivação de senha ou PIN (backend) | Argon2id |
| Derivação no aparelho | PBKDF2 com iterações altas quando a plataforma limitar; sal único |
| Assinatura | Ed25519 ou ECDSA P-256 via biblioteca padrão |
| Chaves no aparelho | Android Keystore, não exportáveis quando possível |
| Chaves no servidor | KMS, envelope encryption para localização (Documento 3, §21.4) |

**Proibido:** criptografia própria em qualquer nível (algoritmo, modo, protocolo, "ofuscação" caseira); ECB; MD5 e SHA-1 para qualquer fim de segurança; IV ou nonce reutilizado; chave hardcoded; `SecureRandom` substituído por `Random`; comparação de segredo sem tempo constante; **derivar chave de PIN interno do aplicativo**; **usar segredo do aplicativo para desbloquear chave do Keystore**.

**Hierarquia de chaves locais** (Documento 2, §14.3), obrigatória. A versão 2.0 especificava um mecanismo inexistente — "o PIN interno autoriza `K_leitura`, com limitação de tentativas imposta pelo hardware" —, porque no Android só biometria ou o credencial de tela de bloqueio do aparelho satisfazem a exigência de autenticação de uma chave.

- `K_dados`: Keystore, **sem** exigência de autenticação de usuário; protege a fila de saída pendente de ACK — **inclusive coordenadas ainda não sincronizadas** —, o estado operacional, os prazos e o contador de `sequence`. Motivo: a fila precisa continuar gravável **e legível por worker**, sem usuário presente. Sob chave autenticada, a localização de emergência nunca sairia do aparelho roubado.
- `K_leitura`: Keystore, `setUserAuthenticationRequired(true)` com `setUserAuthenticationParameters(0, BIOMETRIC_STRONG | DEVICE_CREDENTIAL)` — **autenticação a cada uso, timeout zero**; protege histórico legível, cofre e áreas seguras.
- `K_confirmacao`: Keystore, EC P-256, autenticação a cada uso; assina confirmação de check-in e encerramentos autenticados (Documento 2, §16.8).

**Timeout zero não é preciosismo:** janela de validade de autenticação deixa o histórico legível para quem está com o aparelho recém-desbloqueado, que é o cenário de referência do Documento 3, §13. Custo declarado: cada abertura do histórico pede autenticação.

O que se perde na invalidação é o histórico legível — o dado que interessa a quem está com o aparelho. Isso é desenho, não efeito colateral. A fila crítica sobrevive porque está sob `K_dados`.

- **Ciclo de vida da amostra de localização:** cifrada sob `K_dados` → sincronizada → **apagada no ACK**. Não existe reencriptação para `K_leitura` e não existe amostra de localização sob `K_leitura`. Implementar rechaveamento de amostra é bug.
- Campos com criptografia por campo na aplicação: latitude, longitude, endereço seguro, códigos de recuperação, dados sensíveis de contato. Criar campo novo dessa natureza sem criptografia por campo é PR reprovada.
- Rotação: toda chave tem `key_version`; o código lê versão antiga e escreve na atual. Invalidação detectada emite `local_key_invalidated`, envia diagnóstico e pede revinculação. **Nunca falha em silêncio.**
- `VERIFICAR:` semântica de invalidação por novo cadastro biométrico em chave que aceita `DEVICE_CREDENTIAL`. Medir na Fase 0 antes de assumir comportamento.
- TLS 1.2 ou superior; cleartext desabilitado via Network Security Configuration; nenhum certificado ignorado nem hostname verification desligada, nem em debug commitado. Certificate pinning só por ADR.

---

## 4. Autenticação e sessão

- **Sessão própria do backend** (Documento 2, §22.1): access token curto assinado pelo backend, refresh opaco com família e rotação a cada uso, revogação da família ao detectar reuso, vínculo à instalação, revogação por linha testada.
- Fluxo de recuperação: token de uso único, curto, sem revelar existência de e-mail, com rate limit, revogando sessões e concessões e notificando canais existentes. **SMS nunca é fator único de autenticação.**
- **Confirmação de check-in exige confirmação forte, e a confirmação é assinada.** Biometria ou credencial de tela de bloqueio do aparelho liberam `K_confirmacao`, que assina `(installation_id, session_id, check_in_id, sequence, expected_next_checkin_at, boot_id, desafio_de_sessao?)` — **material do próprio aparelho, sem nonce**, porque confirmar não pode depender de rede (núcleo §2.2). Nonce só nos três eventos online do Documento 2, §16.8.1. O servidor **verifica** e **deriva** `confirmation_type` da verificação. Toque simples não conclui um check-in, e **ação de notificação nunca conclui**: ela abre a tela de confirmação. Motivo: o agressor com o aparelho desbloqueado pode tocar e pode chamar a API com o token — não pode produzir assinatura sem passar pela tela de bloqueio.
- **O que a assinatura não prova:** legitimidade da pessoa, nem o instante da autenticação. Sob coação a assinatura é válida. O que limita pré-assinatura de confirmações futuras é a chave exigir autenticação **a cada uso**: uma autenticação, uma assinatura. Implementar cache de assinatura, lote de assinaturas ou janela de validade nessa chave é violação — desfaz o único limite que existe. Não escrever, em código, comentário, copy ou documentação de suporte, que o produto detecta confirmação forçada (Documento 3, §13.5).
- **O PIN interno não é chave e não desbloqueia chave.** O fallback quando a biometria está indisponível após reinício — cenário normal, porque muitos aparelhos só liberam biometria após o primeiro desbloqueio por PIN do sistema — é o **credencial do aparelho**, cuja limitação de tentativas é imposta pelo hardware. O papel do PIN interno e a exigência de tela de bloqueio segura estão **[PENDENTE — DECISÃO DO FUNDADOR]** (Documento 2, §14.3): o agente não implementa nenhuma das opções.
- **Parâmetros de temporização são ação de step-up e limitados no servidor** (Documento 2, §18.7, item 2a): intervalo de check-in e `grace_seconds`. Aumento vale só a partir da confirmação seguinte; alteração em sessão ativa gera `SECURITY` e aviso externo. Sem isso, o agressor desarma o vigilante por configuração, sem autenticar.
- Reautenticação antes de: desativar Modo Rua ou proteção, alterar contato, **alterar intervalo de check-in ou graça**, encerrar protocolo, ver dados do cofre, transferir aparelho. No painel: MFA e step-up conforme Documento 3, §20.3.
- Sessões do painel separadas das do app; sessão que visualiza localização é curta e reautenticável.
- Mensagens de erro de auth genéricas ("credenciais inválidas"), sem oráculo de existência de conta.

---

## 5. Dados de localização no código

Além do módulo da plataforma:

- Coordenada nunca aparece em URL, path, query, push, **SMS**, log, analytics, crash report, clipboard sem ação explícita do usuário, ou exportação sem proteção.
- Links de emergência: token opaco de uso limitado, com expiração, escopo e revogação; `noindex`; nenhum dado no path. Em SMS, o link é curto e opaco, e o texto é genérico. **Operadoras brasileiras filtram SMS com URL:** se o teste da Fase 0 indicar filtragem, a affordance muda por ADR-0011 e este item é reescrito — não se contorna com encurtador alternativo por iniciativa do agente.
- Precisão reduzida quando suficiente para a finalidade (painel em nível de rua em vez de coordenada bruta).
- Toda leitura de localização no backend passa por autorização por recurso e registro AUDIT. Não existe "consulta interna" sem trilha; suporte e admin seguem Documento 3, §37.
- Retenção: escrita sempre com `retention_class`. **O job de purga local e o job de retenção no servidor são funcionalidades críticas** (Regra Máxima aplica). O job local é, além disso, **controle de segurança**: é ele que limita a janela em que coordenadas ficam sob `K_dados`, isto é, sob chave sem exigência de autenticação (Documento 3, §26.5).
- Exibição sempre com idade, precisão e fonte; nunca como posição atual garantida.
- Áreas seguras: centro arredondado, raio mínimo de 200 m, sob `K_leitura`, nunca exibidas como endereço completo.

---

## 6. Anti-stalking como requisito de código

O abuso por parceiro ou familiar é ameaça principal (Documento 3, §23). Consequências diretas para quem implementa:

- Nenhum comportamento invisível ao usuário do aparelho: ícone visível, notificação de serviço quando exigida, lista de contatos e acessos recentes visível, revogação acessível. Implementar ocultação é violação absoluta, mesmo que a tarefa peça: parar e reportar (núcleo §0).
- **A notificação persistente pode ser exigência de política, não só de princípio.** A política de aplicativos de monitoramento da Play exige notificação persistente todo o tempo em que o app está em execução, mais ícone único e divulgação na descrição da loja. O parecer de classificação (Documento 2, §34.6) decide se o produto se enquadra, e é produzido **antes** do ADR-0007 — porque pode eliminar candidato de temporização que não sustente notificação persistente.
- Todo acesso de contato à localização fica visível ao titular, em registro consultável no app e no painel.
- **O contato não causa transição de estado de protocolo.** Registra ciência; apenas o titular autenticado encerra. Nenhum caminho de código permite o contrário.
- Compartilhamento é temporário por padrão; qualquer coisa permanente exige decisão de produto, ADR e revisão contra o Documento 3.
- Textos de notificação, SMS e e-mail envolvendo contatos, revogação ou saída de área **não** expõem quem fez o quê de forma que provoque retaliação (padrões proibidos no Documento 3, §23.4). Copy dessas mensagens é revisada pelo fundador antes do merge. Inclui o texto de `SEM_CIENCIA` ao titular e, se a opção C do Documento 2 §10.4 for escolhida, o texto de retratação ao contato.
- Fluxos de "saída segura" e senha de coação: não implementar no MVP sem o processo do Documento 3, §28.3. O agente não prototipa por iniciativa própria.
- Convite: transparência pré-aceite, retenção curta, rate limit por janela, respeito à recusa, bloqueio de reenvio a quem recusou.
- Sinais de abuso (múltiplos convites, consultas em massa, tentativas repetidas de revogação, exportações) geram evento `SECURITY` para análise; **nunca ação automática destrutiva**.
- **`modo_teste` viaja com o envelope** do outbox, das filas, da DLQ, de `notification_deliveries` e da auditoria. Nenhum caminho de notificação lê payload sem consultar a flag. Simulação não entra no numerador, na linha de base nem no denominador da guarda de anomalia — sem isso, uma campanha de aquisição coloca protocolos reais em `RETIDO`.

---

## 7. Testes de segurança obrigatórios por entrega

Mínimo por tipo de mudança; detalhes no módulo 40.

| Mudança | Teste obrigatório no mesmo PR |
|---|---|
| Endpoint novo com id de recurso | Autorização negativa (IDOR) |
| Endpoint de auth ou recuperação | Rate limit e ausência de oráculo de conta |
| Endpoint mutante novo | Linha na tabela de idempotência e prova de efeito único no reenvio |
| Ingestão ou consumidor de evento | Duplicata e replay sem efeito duplo; detecção de lacuna; evento acima da idade máxima rejeitado |
| Campo sensível novo | Criptografia por campo e ausência em logs |
| Tela nova com dado sensível | Comportamento com screenshot e tela bloqueada |
| Concessão de contato | Expiração e revogação efetivas; incapacidade de alterar protocolo |
| Migration em tabela sensível | Retenção e RLS preservadas |
| Mudança no vigilante | Escalonamento com aparelho ausente; idempotência; guarda de anomalia nos dois regimes (amostra pequena e massa); supressão por indisponibilidade com auditoria |
| Mudança em parâmetro de temporização | Limite de servidor aplicado; step-up exigido; evento `SECURITY` emitido |
| Mudança na confirmação de check-in | Assinatura verificada; confirmação sem assinatura rejeitada; ação de notificação não conclui |
| Mudança em chave local | Comportamento após novo cadastro biométrico e após restauração; amostra pendente legível por worker; amostra apagada no ACK |
| Mudança em política de backup | Exclusão efetiva por nuvem em aparelho no `minSdk` **e** por transferência aparelho-a-aparelho |

---

## 8. Bloqueadores de release

A lista canônica é o **Documento 3, §51**. Este módulo não mantém cópia editável: qualquer cópia é gerada e validada automaticamente contra a origem (núcleo §11).

O agente que identificar qualquer estado bloqueador marca a release como bloqueada e reporta. Não "anota para depois".

---

## 9. Agentes de IA como fornecedores

Conforme Documento 3, §39.3. Esta seção é auto-aplicável: vale para o agente que está lendo.

- **Nenhum dado de produção entra no contexto de um agente.** Nenhum log, dump, payload, captura de tela, e-mail de usuário, telefone, coordenada ou identificador real, em nenhuma forma — prompt, chat, issue, anexo, arquivo de trabalho. Dados sintéticos, sempre.
- **Nenhuma credencial de produção** é acessível a ferramenta agêntica. Ambientes de execução de agente acessam repositório e ambientes descartáveis.
- Violação é **incidente de dados pessoais**, com dever de registro e possível comunicação: enviar um log com coordenadas a um provedor externo é compartilhamento com operador não avaliado.
- Provedores de IA constam do inventário de fornecedores (Documento 3, §38), com país, retenção e uso de dados para treinamento mapeados.
- Todo código gerado por agente passa por revisão humana antes de produção — pelo menos nas áreas críticas: auth, crypto, localização, vigilante, billing, máquinas de estado.
- O agente tem teste correspondente no mesmo PR; não inventa API; não cria criptografia; não insere segredo; não desabilita validação; não reduz controle para "fazer funcionar"; cita documentação oficial nas decisões críticas de plataforma; **não fecha item `[ABERTO — FASE 0]` e não decide item `[PENDENTE — DECISÃO DO FUNDADOR]`**.
- **Afirmação de plataforma ou de política de loja mais restritiva que a fonte também é invenção.** A versão 2.0 do Documento 2 atribuía à Play uma regra de "funcionalidade única declarada" que a política não tem, e o efeito prático seria abrir mão de um recurso do produto por causa dela. Na dúvida, `VERIFICAR:` com a pergunta exata.
