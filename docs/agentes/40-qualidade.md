# Documento 4 — Módulo 40: Qualidade e testes

**Versão:** 2.2 | **Substitui:** módulo 40 v1.0, v2.0 e v2.1 (correções ARB2 e ARB3)
Pressupõe `00-nucleo.md`. Estratégia de referência: Documento 2, §33.

---

## 1. Pirâmide e responsabilidade de cada camada

| Camada | Cobre | Não cobre |
|---|---|---|
| Unitário | Máquinas de estado (as três: sessão, protocolo e **cobertura**), regras de tempo, idempotência, autorização, validação, mapeamentos, reconciliação, precedência de comandos | Room real, HTTP real, UI |
| Integração | Room, DAOs e migrations; API com PostgreSQL, Redis e fila em containers efêmeros; outbox; vigilante; billing e FCM falsos | Aparelho real |
| Instrumentado (Android) | Compose de fluxos críticos, permissões, notificações, agendamento, reboot e reidratação, migrations Room em device, **Keystore com autenticação a cada uso**, **exclusão de backup** | Regra de negócio, já coberta em unitário |
| E2E | Jornadas: ativação, ausência de resposta, alerta, ciência do contato, acionamento manual, simulação, recuperação, compra, cancelamento | Casos exaustivos |
| Manual em aparelho físico | O que emulador não prova: fabricante, bateria, tela bloqueada, force-stop, campo, **entrega de SMS por operadora** | Substituir as camadas acima |

Regra: o teste vive na camada mais baixa capaz de provar o comportamento. Testar regra de negócio via E2E é reprovado por lentidão e fragilidade.

---

## 2. Regras de escrita de teste

- **Determinístico ou não existe:** relógio fake (`Clock` injetado), dispatchers de teste, sem `sleep`, sem depender de ordem de execução, sem rede real em unitário ou integração. Teste flaky é bug P1: corrigir ou remover com issue, nunca conviver.
- **Nunca aguardar tempo real para lógica de tempo:** avançar o clock fake. Testar obrigatoriamente: prazo expirado durante kill do app; **prazo atravessando reboot, com `boot_id` diferente**; relógio adiantado; relógio atrasado; mudança de fuso; eventos fora de ordem, duplicados e atrasados; **evento acima da idade máxima**. Para código de sync e ingestão, duplicata e desordem são **casos obrigatórios**.
- Estrutura dado/quando/então; um comportamento por teste; nome descreve o comportamento (`checkIn_expiraDuranteReboot_emiteMissedSemEscalar`).
- Fakes em vez de mocks para plataforma (localização, notificações, rede, billing, SMS): mock de framework gera teste que espelha implementação. Mock pontual só para verificar interação essencial.
- Todo teste tem asserção significativa. Teste sem asserção, ou que só verifica "não lançou exceção" onde há contrato de resultado, é reprovado.
- **Teste de máquina de estados:** tabela de transições completa das **três** máquinas (Documento 2, §10.1, §10.2 e §11.2), transições inválidas relevantes verificadas, e a **matriz de precedência** do §10.3 coberta caso a caso. A cobertura era o único estado visível ao usuário sem tabela e por isso sem obrigação de teste, embora seja bloqueador de release. Recomendado mutation testing em `domain:streetmode` para provar que os testes matam mutantes.
- **Testes obrigatórios da confirmação assinada:** assinatura produzida e verificada **sem rede**, com a chave pública local (é assim que o critério da Fase 1 é verificável sem backend); **uma autenticação produz exatamente uma assinatura**; reenvio da mesma confirmação não repete efeito; `sequence` que não avança é rejeitada; desafio de sessão desconhecido é rejeitado, desafio conhecido mas não corrente é aceito, ausência de desafio é aceita quando a instalação nunca registrou sessão; nonce exigido nos três eventos online e **não** exigido no check-in.
- **Testes de reconciliação de `policy_version`:** valor local acima do limite vigente é reduzido ao limite na primeira comunicação, com evento e aviso; alteração de limite não encurta prazo de sessão em curso.
- **Teste de texto de estado:** cada estado de cobertura exibe o texto próprio do Documento 2, §11.1. Exibir o texto de `COBERTURA_LOCAL` em `COBERTURA_SUSPENSA` é falha de teste, não detalhe de copy: naquele estado o vigilante está armado e o alerta vai ocorrer.
- Dados de teste sintéticos. **Nunca dado pessoal real em fixture** — a proibição de dado de produção do módulo 30, §9 vale também para testes.

---

## 3. Política de cobertura

Os pisos abaixo **são gates de CI**. A exigência qualitativa é adicional, não alternativa. Os números não devem subir sem motivo registrado; a advertência clássica contra transformar cobertura em meta não autoriza tratá-los como sugestão.

| Área | Piso (branch) | Exigência qualitativa |
|---|---|---|
| `domain:*` (máquinas de estado, tempo, protocolo, cobertura) | 90% | Todas as transições das três máquinas — **incluindo as quatro de `SEM_CIENCIA` e a entrada de `COBERTURA_INDISPONIVEL`** — e a matriz de precedência; casos de desordem e duplicata |
| Sync engine e ingestão de eventos | 90% | Idempotência, replay, detecção de lacuna e idade máxima provados |
| **Vigilante** | 90% | Escalonamento com aparelho ausente; idempotência; guarda de anomalia nos dois regimes; limite de parâmetros vindos do aparelho; supressão por indisponibilidade |
| **Chaves locais e confirmação assinada** | 90% | Autenticação a cada uso; amostra pendente legível por worker; assinatura verificada no servidor |
| Autorização (backend) | 100% dos endpoints com id, com teste negativo | Por endpoint, não por linha |
| **Idempotência (backend)** | 100% dos endpoints mutantes, com teste de reenvio | Por endpoint. Endpoint sem linha na tabela do Documento 2, §16.3 falha o build |
| Camada de dados (repos, mappers, DAOs) | 80% | Migrations testadas |
| ViewModels | 70% | Estados degradado, erro e cobertura reduzida cobertos |
| Composables e UI pura | sem piso numérico | Fluxos críticos com instrumentado e previews de estados |

Regras:

- PR que **reduz** cobertura de área crítica precisa justificar na descrição.
- Proibido excluir arquivo ou classe do relatório de cobertura sem comentário justificando e issue.
- Cobertura menor é aceitável apenas em: spike marcado como descartável (nunca promovido a produção sem reimplementação), wrapper fino de SDK encapsulado, código puramente visual. **Nunca** aceitável em: segurança, tempo, sync, vigilante, billing, migrations, chaves.

---

## 4. Testes de rede e resiliência

Obrigatórios para qualquer código que fale com a API: offline total; latência alta; perda de pacote; timeout; DNS falho; resposta parcial; 429 com retry correto; 500; certificado inválido (deve falhar fechado); retorno de rede disparando sync.

No backend e no vigilante: banco indisponível; fila indisponível; FCM falhando; **provedor de SMS falhando**; webhook duplicado; **indisponibilidade do próprio backend durante prazos vencidos** — deve suprimir, reagendar **com jitter** e auditar cada supressão, e a **recuperação não pode abrir protocolos em massa**; **falha correlacionada em massa** (deve acionar a guarda de anomalia e retenção, não acionar milhares de contatos); **guarda de anomalia com base de 30 sessões**, provando comportamento definido em amostra pequena; **200 simulações em uma hora sem afetar numerador, linha de base ou denominador**; **expiração de entitlement com sessão ativa e com protocolo aberto** (não interrompe); **transferência de aparelho com sessão ativa e com protocolo aberto** (não desarma o vigilante); **tentativa de estender prazo ou graça além do `policy_version`** (rejeitada, com o prazo anterior preservado).

**Teste de carga sintético de ingestão** dimensionado para 100 mil usuários, executado na Fase 2 e repetido antes do beta público, com `EXPLAIN` do caminho crítico registrado.

**Teste de carga do vigilante**, na Fase 4, dimensionado para 100 mil usuários, em dois cenários: pico sazonal de deslocamento e **tempestade de recuperação** após indisponibilidade própria. O vigilante era o único componente sem teste de carga, sendo o único cuja falha produz alerta falso em massa. `EXPLAIN` da seleção de prazos registrado.

**Medição de ponta a ponta**, na Fase 4: do prazo vencido à primeira ciência do contato, comparada ao teto do orçamento do Documento 2, §12.5. Medir os termos isoladamente não diz qual tempo o produto promete.

---

## 5. Matriz de aparelhos e evidência de campo

Matriz mínima (Documento 2, §33): Samsung intermediário; Motorola intermediário; Xiaomi ou Redmi; **dois aparelhos abaixo de API 33, um deles no `minSdk`**; um no Android atual, que hoje é **17**; um com pouca RAM; um em economia extrema de bateria. Motivo dos dois abaixo de API 33: a faixa `minSdk` 30 → `targetSdk` 36 ramifica em notificações, backup, tipos de serviço em primeiro plano e alarmes exatos, e um aparelho não caracteriza três gerações de sistema.

- Nenhuma funcionalidade de background, localização, notificação, boot ou chave é considerada implementada sem passar na matriz aplicável.
- **A evidência de campo segue os dois níveis do núcleo §3.1:** automatizada por PR; física por marco de fase e sempre que a mudança **alterar comportamento observável** de background, localização, notificação, Keystore, boot ou adaptação de fabricante. **O rótulo `aguardando-evidencia-campo` é aplicado automaticamente pelos caminhos alterados** (núcleo §3.1), não pelo julgamento do autor; o humano só pode removê-lo com justificativa registrada na PR.
- Testes de campo por release relevante: caminhada, transporte, metrô e garagem (perda de GPS), alternância Wi-Fi e dados, modo avião, tela bloqueada, bateria baixa, app morto pelo sistema, **force-stop pelo gerenciador do fabricante**, reboot.
- **Medições obrigatórias na Fase 0**, com **método escrito e revisado antes da coleta** e template do núcleo §9 preenchido por executor humano: atraso de disparo do pedido de confirmação em p50, p95 e **p99** por fabricante — o p99 define a `margem_de_rede`; latência de transição de cerca de proximidade; consumo em sessão, com e sem heartbeat; latência de detecção de aparelho inalcançável; **latência de execução de worker com e sem serviço em primeiro plano ativo, em Android 16 e 17** (efeito da cota de jobs); efeito de invalidação de chave após novo cadastro biométrico, após restauração e em chave que aceita credencial de dispositivo; **entrega de SMS com link curto nas três principais operadoras, com taxa e tempo até entrega por operadora**.
- **Re-medição na Fase 4**, com o código de produção, do atraso de disparo em p99 por fabricante. O mecanismo do spike não é o de produção — este nasce com `Clock` injetado, WorkManager sob as regras do módulo 10 e sujeito à cota de jobs do Android 16 —, logo o p99 do spike não é o p99 do produto. Divergência acima do limiar declarado no ADR-0007 obriga revisão do ADR e da `margem_de_rede`.
- **Testes de backup** (instrumentado e físico): exclusão efetiva por backup em nuvem em aparelho no `minSdk`, com `fullBackupContent`, **e** por transferência aparelho-a-aparelho, com `<device-transfer>`. Em ambos, provar que a instalação restaurada recebe identidade nova e contador de `sequence` novo. Sem o segundo, duas instalações compartilham `installation_id` e a deduplicação inteira deixa de funcionar.
- Resultado de campo registrado com o template do núcleo §9 e arquivado em `docs/evidencias/`.

---

## 6. Performance e bateria

- Macrobenchmark: startup frio e quente; Baseline Profiles gerados no pipeline.
- Limiares de bloqueio: regressão de startup frio acima de 10% bloqueia merge; consumo de sessão acima do orçamento bloqueia; ANR novo em faixa interna bloqueia promoção de release. **Os valores de bateria e de falso positivo estão `[ABERTO — FASE 0]`** e entram por ADR-0012, com valores provisórios explicitamente marcados até então.
- **Falso positivo é medido em duas camadas:** interno (escalonamento indevido que não acionou contato) e externo (contato acionado indevidamente). Apenas o externo bloqueia release.
- Todo PR que toca coleta de sinais, workers, agendamento ou localização declara impacto esperado de bateria e como foi verificado. O heartbeat entra nessa conta: é temporizador de rede periódico durante toda a sessão, com cadência provisória de 15 minutos (ADR-0005).

---

## 7. Gates de CI

Pipeline conforme Documento 2, §32; lista de gates no núcleo §11. Regras de comportamento:

- Main vermelha para tudo: prioridade máxima é restaurar, por revert imediato se a correção não for óbvia.
- Proibido merge com teste desabilitado para passar; proibido rerun até passar como estratégia para flaky.
- Gates Android: lint, Detekt e ktlint com regras customizadas, unit, build, instrumentados em dispositivo gerenciado, grafo de módulos, análise de dependências, cobertura com os pisos da §3, **lint de manifesto exigindo os dois mecanismos de exclusão de backup, incluindo `<device-transfer>`**.
- Gates backend: lint, typecheck, unit, integração com containers efêmeros, migrations em banco efêmero, verificação de teste negativo de autorização por endpoint, **verificação de linha na tabela de idempotência por endpoint mutante**, **verificação de que nenhum caminho de notificação lê payload sem consultar `modo_teste`**, scan de imagem e dependências, smoke em staging.
- Secrets scanning e SAST em ambos; achado crítico bloqueia.
- Marcadores: `VERIFICAR:` em código de produção falha o build; `TODO` sem issue válida falha o build; **ocorrências de `[ABERTO — FASE 0]` e `[PENDENTE — DECISÃO DO FUNDADOR]` em `docs/` conferidas contra as tabelas do núcleo §0**.
- Listas canônicas: toda reenunciação é **gerada** e validada contra a origem — bloqueadores de release (Documento 3, §51), eventos auditáveis (§34.1), ações de step-up (§20.3), módulos do backend (Documento 2, §18).
- Rótulo de evidência: `aguardando-evidencia-campo` aplicado por caminho alterado; remoção exige justificativa registrada.
- Artefatos: AAB assinado fora do repositório; proveniência registrada.
- Nenhum gate é desativado sem ADR e issue de reativação.
