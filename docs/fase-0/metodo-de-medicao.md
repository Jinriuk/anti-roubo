# Fase 0 — Método de medição

**Estado:** PROPOSTO — **exige revisão e aceite antes de qualquer coleta**
**Data:** 2026-07-26 · **Atualizado:** 2026-07-27, com as decisões D1 a D9 do fundador
**Autor:** agente. Não é o executor das medições.

> **Por que este documento existe antes de qualquer código.**
> Documento 5, Fase 0: *"o método de medição é escrito e revisado **antes da coleta**, não
> declarado depois por quem mediu"*. Medição acrescentada depois custa recoleta em aparelho
> físico — por isso a lista precisa estar fechada antes da primeira sessão de coleta.

> ✅ **As condições que bloqueavam este método foram resolvidas em 2026-07-27.** O núcleo entrou
> no repositório e o **template de evidência (§9)** está disponível; a **faixa de intervalo** foi
> declarada como valor provisório com ADR dono; a **matriz de aparelhos** foi corrigida e o
> hardware existe; as medições do ARB4-007 foram incorporadas ao Documento 5.
> Resta a **revisão e o aceite deste método pelo fundador**, e a definição do **timebox da fase**
> — que o §4 dimensiona.

---

## 1. Papéis

| Papel | Quem | Regra |
|---|---|---|
| **Executor** | humano identificado, nomeado por medição | O template do núcleo §9 é literal: *"Executor: (humano identificado; **o agente não executa teste físico e não preenche resultado observado**)"*. Não é interpretação nem preferência — é o campo do formulário |
| **Agente** | prepara tudo o que antecede a coleta | Escreve o método, o instrumento de `spike/`, os testes automatizáveis, os entregáveis de mesa e as propostas de ADR. Verifica exaustivamente o que é verificável sem aparelho. **Não preenche resultado observado** |
| **Revisor do método** | fundador | Revisa e aceita **este documento** antes da coleta. É o que separa método de racionalização |
| **Redator do instrumento** | agente | Escreve o código de `spike/` que produz os números, sob ADR-0003 |
| **Arquivista** | executor | Registra em `docs/evidencias/` com o template do núcleo §9, e o comportamento por marca em `docs/fabricantes/<marca>.md` |

**Evidência inventada é falha crítica.** Número não observado não entra em nenhuma tabela, nem
como estimativa, nem como "provisório". Célula sem coleta fica declarada vazia.

## 2. Matriz de aparelhos

Dependências declaradas (Documento 5, Fase 0, corrigido na 4ª rodada): **quatro ou mais**
aparelhos reais — Samsung, Motorola, Xiaomi ou Redmi —, **dois deles abaixo de API 33**, um no
`minSdk` provisório 30, **um em Android 16 e um em Android 17**.

| ID | Marca | Android / API | Papel |
|---|---|---|---|
| A | Samsung intermediário | **11 (API 30)** | `minSdk`; caminho de pré-condição de notificação sem `POST_NOTIFICATIONS`; `fullBackupContent` |
| B | Motorola intermediário | **12 ou 12L (API 31/32)** | segundo aparelho abaixo de API 33; `dataExtractionRules` já existe |
| C | Samsung, Motorola, Xiaomi ou Redmi | **16 (API 36)** | primeira versão com cota de job a partir de FGS — metade da M9 |
| D | Xiaomi ou Redmi | **17 (API 37)** | Android atual; gerenciador de tarefas mais agressivo do conjunto |

✅ **Lacuna resolvida.** A matriz anterior declarava três aparelhos com apenas um no Android
atual, e a M9 exige **Android 16 e 17**. Com aquela configuração a medição não era executável em
nenhuma das duas versões. O Documento 5 foi corrigido e o hardware existe. A comparação "por
fabricante" da M9 fica limitada aos aparelhos C e D e a limitação é declarada na evidência, em
vez de simulada.

### Estados de bateria

Toda medição marcada "por estado de bateria" roda em **dois** regimes, nunca simultâneos no
mesmo aparelho:

1. **Normal** — sem otimização agressiva, sem economia de bateria, aplicativo sem isenção.
2. **Economia extrema** — modo de economia do fabricante ativo, com a configuração de fábrica.
   **O usuário nunca é instruído a desativar toda a proteção de bateria** (Documento 2, §35);
   medir com ela ligada é medir o produto real.

## 3. Controles de ambiente — registrados em toda coleta

Sem estes campos, dois números da mesma medição não são comparáveis, e a exigência de
*"consumo medido com método declarado e comparável"* não é atendida.

`aparelho` · `marca` · `modelo` · `versão do Android` e `nível de API` · `build do fabricante`
(One UI / HyperOS / MIUI, com versão) · `estado de bateria` (normal / economia extrema) ·
`isenção de otimização de bateria` (concedida / negada) · `conjunto de permissões concedidas` ·
`estado do canal check_in` · `Não Perturbe` (ligado / desligado) · `rede` (Wi-Fi / móvel /
avião / sem sinal) · `operadora` · `versão do spike` (commit) · `intervalo de check-in
configurado` · `data e hora de início e fim, em UTC` · `executor`.

## 4. Dimensionamento — e o que ele custa em calendário

A medição **M1** exige **p99**, e o p99 é o parâmetro mais caro do projeto: ele define a
`margem_de_rede` do vigilante (Documento 2, §18.7) e entra no orçamento de latência do §12.5.

### Faixa de intervalo — valor provisório declarado

O intervalo de confirmação é escolhido pelo usuário entre os limites do `policy_version`
(Documento 2, §12.5, termo 1), e esses limites pertencem ao **ADR-0005-A**, cujo prazo é "antes
da Fase 1" — depois desta medição. O núcleo §0 resolve o impasse: *"onde uma regra precisar de
número antes do ADR, o número é **valor provisório declarado como tal**, com o ADR dono
identificado."*

> **Faixa provisória: mínimo 15 min · padrão 30 min · máximo 60 min. Dono: ADR-0005-A.**
> Não é decisão fechada e não vira permanente por uso. Alargar o máximo depois **obriga
> re-medir no novo máximo**, porque o p99 não se extrapola.

Justificativa da faixa: abaixo de 15 min o produto entra no risco que o Documento 1, §8.3
descreve — o usuário ignora, desativa ou confirma sem atenção —, agravado por este desenho exigir
**autenticação a cada confirmação**. Acima de 60 min a janela de detecção passa a dominar a
promessa do produto. 30 min como padrão é o ponto em que uma sessão típica de deslocamento
produz poucas confirmações.

### Amostra e calendário

**Amostra por célula:** **n ≥ 200**, com intervalo de confiança declarado ao lado do número.
Com n = 200 o p99 é o 2º pior valor observado — estimativa frágil, e a fragilidade é declarada,
não escondida. O piso de 200 (e não 300) é defensável **porque o próprio Documento 5 declara que
o p99 do spike não é o p99 do produto** — o mecanismo de produção nasce diferente e a Fase 4
**re-mede com o código de produção**. Abaixo de 200 não existe p99, existe pior caso, e
chamá-lo de p99 seria evidência inventada.

**Onde o p99 é exigido:** apenas no **máximo da faixa (60 min)**. Intervalo mais longo produz
janela de inatividade mais profunda e, portanto, o pior atraso — que é o que a `margem_de_rede`
precisa cobrir. Nos demais pontos bastam p50 e p95, com n ≥ 100.

**Células:** 3 fabricantes × 2 estados de bateria. Os estados são sequenciais no mesmo aparelho;
os aparelhos correm em paralelo.

| Ponto | Estatística | n | 2 estados, sequenciais | Calendário |
|---|---|---|---|---|
| 60 min (máximo) | p50, p95, **p99** | 200 | 400 h | **~16,7 dias** |
| 30 min (padrão) | p50, p95 | 100 | 100 h | ~4,2 dias |
| 15 min (mínimo) | p50, p95 | 100 | 50 h | ~2,1 dias |
| | | | **total** | **~23 dias de coleta contínua** |

> **Consequência para o timebox.** O Documento 5, §2 determina que **fase sem timebox não
> inicia**. A M1 é o termo dominante do timebox da Fase 0, e ela é dominada pelo **máximo** da
> faixa. Reduzir o máximo provisório de 60 para 30 min cortaria a M1 de ~23 para ~14 dias. É a
> alavanca que o fundador tem, e ela é de produto, não de engenharia.

**Não extrapolar.** Medir num intervalo curto e projetar para um longo é inválido: o
comportamento do agendador muda com a duração da janela de inatividade, e é exatamente esse
efeito que a medição existe para capturar. Reportar p50/p95/p99 **por ponto**, nunca uma média
entre pontos.

## 5. Medições obrigatórias

As onze da tabela do Documento 5, Fase 0. Cada uma com definição operacional, instrumento,
procedimento, saída e critério de descarte.

---

### M1 · Atraso de disparo do pedido de confirmação

**Define a `margem_de_rede`. É a medição mais consequente da fase.**

- **O que é medido:** intervalo entre o instante em que o prazo de check-in vence
  (`expected_next_checkin_at`, por relógio monotônico) e o instante em que a notificação de
  pedido de confirmação é efetivamente **postada** pelo sistema.
- **O que não é medido:** o tempo até o usuário ver, tocar ou confirmar. Isso é M12.
- **Instrumento:** `spike/temporizacao/`. Cada disparo grava `(prazo_previsto_elapsed,
  prazo_real_elapsed, boot_id, candidato, controles da §3)` em arquivo local, apenas com
  dados sintéticos.
- **Procedimento:** sessão contínua com o intervalo declarado; aparelho sem interação do
  usuário durante a janela; tela bloqueada; sem carregador (o carregador altera o regime de
  economia e invalidaria a célula).
- **Repetição por candidato:** a medição roda para **cada candidato de temporização
  sobrevivente** do Documento 2, §12.2 — e a lista de sobreviventes depende do parecer de
  monitoramento, ver §7.
- **Saída:** p50, p95 e **p99**, por fabricante e por estado de bateria, com **n e intervalo de
  confiança declarados**.
- **Descarte:** célula com n < 300; célula em que houve interação do usuário, reboot não
  planejado ou mudança de configuração no meio; célula com carregador conectado.

### M2 · Sobrevivência a reboot

- **O que é medido:** após reinício, a sessão é reidratada e o prazo é reavaliado sem
  intervenção do usuário — e em quanto tempo.
- **Instrumento:** `spike/persistencia/` com `BootReceiver`.
- **Procedimento:** reboot com sessão ativa e prazo futuro; reboot com prazo **já vencido**
  durante o desligamento; para cada **conjunto de permissões** relevante (todas concedidas;
  sem isenção de bateria; sem localização em segundo plano; sem alarme exato, se aplicável).
- **Saída:** `possível` / `parcial` / `não comprovada`, **por conjunto de permissões e por
  fabricante**, com o tempo até a retomada e a descrição do que falhou quando `parcial`.
- **Nota de plataforma:** `location` **não** está na lista de tipos proibidos a partir de
  `BOOT_COMPLETED` — é esse fato que decide se o `BootReceiver` pode retomar a sessão com
  serviço (Documento 2, §35). A medição confirma em aparelho.

### M3 · Sobrevivência a force-stop e a gerenciador de tarefas do fabricante

- **O que é medido:** o que volta a executar, e quando, após (a) force-stop pelas configurações
  do sistema e (b) encerramento pelo gerenciador de tarefas do fabricante — que não são a mesma
  coisa e precisam de linhas separadas.
- **Saída:** `possível` / `parcial` / `não comprovada`, por conjunto de permissões e por marca,
  com o gatilho que reativa (abertura do app, boot, push, nenhum).
- **Consequência de produto que a medição alimenta:** retomada impossível gera **aviso honesto
  ao usuário e evento ao servidor**, que passa a registrar perda de cobertura (Documento 2, §35).

### M4 · Latência de transição de cerca de proximidade

- **O que é medido:** intervalo entre a travessia física do limite da cerca e a entrega do
  evento de transição ao aplicativo.
- **Procedimento:** raio declarado por ensaio; travessia a pé e de carro; **e comportamento
  após reboot** — a cerca sobrevive ao reinício ou precisa ser reregistrada.
- **Saída:** p50 e p95, por fabricante. Sem exigência de p99.
- **Descarte:** ensaio sem confirmação independente do instante da travessia.

### M5 · Consumo em sessão de nível econômico

- **O que é medido:** consumo de bateria atribuível ao aplicativo durante sessão ativa no nível
  `economico`.
- **Método declarado — é o que torna o número comparável:** medição por diferença de percentual
  de carga em janela de duração fixa (mínimo 4 h), com tela desligada, sem interação, sem
  carregador, partindo de um mesmo nível inicial de carga declarado; **mais** a leitura do
  atribuído ao aplicativo pelo próprio sistema, registrada como segunda coluna e não como
  substituta da primeira. Ambas as colunas vão para a evidência.
- **Saída:** % por hora **e** % do ciclo diário estimado, por fabricante e estado de bateria,
  com o método acima citado por extenso na evidência.
- **Descarte:** janela com tela ligada, interação, carregador, ou mudança de rede no meio.

### M6 · Notificação com Não Perturbe e tela bloqueada

- **O que é medido:** o que o usuário efetivamente vê e ouve, por canal, com Não Perturbe ligado
  e desligado, com tela bloqueada e desbloqueada.
- **Saída:** descritivo por fabricante, incluindo captura **de conteúdo sintético**, cobrindo:
  a notificação aparece; produz som ou vibração; o conteúdo em tela bloqueada é o genérico
  esperado; a ação de confirmação antecipada aparece; a ação de acionamento manual aparece.
- **Regra que a medição verifica, e que não pode falhar:** a ação de notificação **abre** a tela
  de confirmação e **nunca** conclui o check-in.

### M7 · Invalidação de chave

- **O que é medido:** o efeito observado sobre `K_dados`, `K_leitura` e `K_confirmacao` após:
  (a) novo cadastro biométrico; (b) restauração do aparelho; (c) remoção do bloqueio de tela.
- **Cobertura obrigatória:** **incluindo chave que aceita `DEVICE_CREDENTIAL`** — é o
  `VERIFICAR:` aberto do Documento 2, §14.3.
- **Saída, por chave e por evento, por fabricante:** a chave sobrevive / é invalidada; a
  invalidação é detectável pelo aplicativo; `local_key_invalidated` é emitido; **nunca falha
  em silêncio**.
- **Consequência que a medição alimenta:** o comportamento de `K_confirmacao` invalidada **com
  sessão ativa** não tem regra escrita em nenhum documento (achado ARB4-009). A medição produz
  o fato; a regra é decisão posterior.

### M8 · Consumo com e sem heartbeat, e latência de detecção de aparelho inalcançável

- **O que é medido:** (a) diferença de consumo entre sessão com heartbeat e sem heartbeat, na
  mesma cadência provisória; (b) segundos entre o aparelho tornar-se inalcançável e a detecção
  ser possível pelo lado do servidor de teste.
- **Cadência provisória declarada:** **15 minutos**, propriedade do ADR-0005 (Documento 2,
  §18.7, item 6). Valor provisório, medido, não fechado aqui.
- **Saída:** % por hora nas duas condições; segundos até detecção. Define a cadência provisória
  do ADR-0005 e alimenta o `silencio_maximo_de_comunicacao` do §11.2.

### M9 · Latência de execução de worker com e sem serviço em primeiro plano ativo

- **O que é medido:** atraso entre o agendamento de um worker diferível e sua execução, em duas
  condições — com o FGS de sessão ativo e sem ele.
- **Por que importa:** em Android 16 e superior, **para todo aplicativo, independente do
  `targetSdk`**, jobs iniciados a partir de um serviço em primeiro plano obedecem às cotas de
  execução, **incluindo os criados por WorkManager**. Sync, purga e retomada disparados a partir
  do FGS de sessão estão sob cota.
- **Saída:** p50 e p95, por fabricante, **em Android 16 e em Android 17**.
- ⚠️ **Não executável com a matriz declarada.** Ver §2.

### M10 · Entrega de SMS com link curto, por operadora

Trabalho de mesa. Não usa `spike/`.

- **O que é medido:** taxa de entrega e **tempo até a entrega** de mensagem com **link curto no
  corpo**, para números das **três principais operadoras**.
- **Por que o link é o objeto do teste:** operadoras brasileiras filtram e bloqueiam SMS
  contendo URL, com regras que variam por remetente, tipo de contratação e reputação — e todo o
  desenho de alerta depende de "texto genérico mais link opaco curto" (Documento 2, §25.1).
- **Procedimento:** para **cada** das três cotações de provedor, enviar o texto real previsto,
  com link curto, para números de teste das três operadoras, em pelo menos três janelas de
  horário distintas; registrar entrega, não-entrega e latência **por operadora e por provedor**.
- **Saída:** matriz operadora × provedor com taxa de entrega e tempo até entrega.
- **Entregável acoplado:** se houver filtragem, **plano B de affordance escrito** — instrução
  para abrir o aplicativo, ou número para retornar —, que muda o ADR-0011 e a página de
  emergência. O plano B é escrito **antes de contratar**, não depois.

### M11 · Parcela do público-alvo sem tela de bloqueio segura

Pesquisa de mercado, não medição de aparelho. Está na tabela do Documento 5 sob um título que
diz "por fabricante e por estado de bateria" — categorização registrada como item **E12**.

- **O que é levantado:** fração do público-alvo (adultos, grandes centros urbanos, usuários de
  banco digital) que **não** usa bloqueio de tela.
- **Para que serve:** é o insumo da decisão `[PENDENTE — DECISÃO DO FUNDADOR]` sobre exigir tela
  de bloqueio segura (Documento 2, §14.3), que por sua vez é **pré-condição de entrada da
  Fase 1**.
- **Saída:** percentual **com n e fonte declarados**. Percentual sem denominador é proibido.

---

## 6. Medições acrescentadas na 4ª rodada

✅ **Incorporadas ao Documento 5 em 2026-07-27** (decisões D3 e D7 do fundador). Não são mais
"ausentes": M12, M13, M14 e M15 constam da tabela de medições obrigatórias da Fase 0 e têm
critério de aceite próprio. Esta seção passa a ser a especificação operacional delas, no mesmo
padrão das onze anteriores.

### M12 · Latência da confirmação: do prompt à assinatura verificada

- **O que é medido:** intervalo entre a abertura do prompt de autenticação e a assinatura
  produzida por `K_confirmacao` e verificada pela chave pública local.
- **Duas condições, medidas separadamente:** com **biometria** e com **credencial de tela de
  bloqueio do aparelho** — o segundo caminho é o normal após reinício, e é mais lento.
- **Saída:** p50, p95, p99, por fabricante, por condição.

### M13 · Custo de bateria de N confirmações por sessão

- **O que é medido:** consumo atribuível ao ciclo completo de confirmação (acordar, notificar,
  prompt, assinar, persistir, sincronizar), multiplicado pela quantidade de confirmações de uma
  sessão típica na cadência provisória.
- **Saída:** % de carga por confirmação e por sessão, por fabricante.

### M14 · Taxa de confirmação dentro do prazo

**É a medição que governa o número que bloqueia release.** O falso positivo **externo** — o
único que bloqueia (módulo 40, §6) — é governado pela fração de usuários que não confirma a
tempo. O mecanismo do §16.8 exige autenticação a cada confirmação, várias vezes por sessão, com
o celular no bolso, no metrô, com a mão molhada.

- **O que é medido:** fração de pedidos de confirmação respondidos dentro de
  `intervalo + graça`, sob as condições adversas que a matriz de campo já nomeia — caminhada,
  transporte, metrô, garagem, elevador, ausência de sinal, bateria baixa, tela bloqueada.
- **Registro obrigatório do motivo da falha:** não viu · viu e não conseguiu autenticar ·
  biometria indisponível · autenticou fora do prazo · notificação não chegou.
- **Saída:** taxa com **n declarado**, por condição. Declarada como insumo do **ADR-0012**.
- **Limite honesto da medição:** o executor é quem escreveu o produto e sabe que está sendo
  medido. O número tem viés otimista **por construção**, e a evidência declara isso — o número
  real só aparece no Beta A.

### M15 · Distribuição de versões do Android no público-alvo

Não vem da ARB4. Vem do **ADR-0002**, cujo segundo critério — dados de mercado brasileiro — não
tinha fonte prevista em nenhuma fase. É a **mesma coleta, na mesma amostra, com o mesmo
instrumento da M11**, e custa uma linha a mais no questionário.

- **O que é levantado:** distribuição de versões do Android entre adultos de grandes centros
  urbanos usuários de banco digital — não a distribuição do Brasil em geral, e não a mundial.
- **Saída:** percentual por faixa de API, **com n e fonte declarados**, com destaque para a
  fração abaixo de **API 33**, que é o corte que o ADR-0002 revisará.
- **Para que serve:** fecha o segundo critério do Documento 2, §39 antes da revisão pós-Fase 0
  do `minSdk`. Enquanto não existir, o `minSdk` 30 permanece escolha por default.

---

## 7. Ordem de execução

A ordem não é preferência: há uma dependência declarada que a inverte.

```text
1. Parecer de classificação como aplicativo de monitoramento          ← trabalho de mesa
   │  Pode eliminar candidato de temporização POR POLÍTICA, não por medição:
   │  a política exige notificação persistente durante toda a execução,
   │  mais ícone único e divulgação na descrição.
   │  Se o produto se enquadrar, o candidato "WorkManager puro" está eliminado
   │  antes de qualquer medição — e medi-lo seria desperdício de calendário.
   ▼
2. Lista final de candidatos a medir  →  define o volume real de M1
   ▼
3. M10 + M11 + M15 (mesa e pesquisa)   ‖  em paralelo, não dependem de hardware
   ▼
4. M2, M3, M6, M7 (ensaios curtos, discretos)  →  eliminam candidatos cedo
   ▼
5. M1, M5, M8, M9, M12, M13 (coleta longa, contínua)  →  dominam o calendário
   ▼
6. M4, M14 (campo, exigem deslocamento)
   ▼
7. ADRs propostos: 0007, 0008, 0011-provisório, 0012, 0005-B
```

## 8. Registro e arquivamento

- Cada medição gera um registro no **template do núcleo §9**, que está disponível em
  `docs/agentes/00-nucleo.md`. Colar preenchido na PR e arquivar em `docs/evidencias/`. O
  template exige, entre outros: nível, fase e item do Documento 5, **status declarado
  (IMPLEMENTADA | NÃO IMPLEMENTADA)**, executor humano identificado, aparelho, cenário
  executado, resultado **esperado** e **observado** em campos separados, divergências,
  limitações conhecidas e decisão.
- Resultado arquivado em `docs/evidencias/`, com data e versão.
- Comportamento divergente por marca vai para `docs/fabricantes/<marca>.md`, com aparelho e
  versão testados.
- Dados brutos: apenas sintéticos, sem dado pessoal real, sem coordenada real de pessoa real.
  A regra do módulo 40 §2 vale também aqui.
- Toda saída percentual declara **n**. Percentual sem denominador é proibido em qualquer
  material, interno ou externo.

## 9. Condições de interrupção que estas medições podem acionar

Documento 5, Fase 0, e Documento 1, §32. Se qualquer uma se confirmar, a fase **para e
redesenha**, em vez de seguir para a Fase 1:

- a solução depender de Accessibility ou de comportamento oculto;
- **nenhum** candidato de temporização entregar atraso compatível com a promessa (M1);
- o consumo for incompatível com uso diário (M5, M8, M13);
- o recurso principal de localização em segundo plano não tiver caminho de aprovação;
- **não existir canal de alerta viável a custo aceitável — inclusive por filtragem de SMS com
  link sem alternativa praticável** (M10).
