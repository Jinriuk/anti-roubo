# Fase 0 — Método de medição

**Versão:** 1.0
**Estado:** ✅ **VIGENTE** — congelado. A coleta pode começar.
**Submetido:** 2026-07-27 · **Aceito pelo fundador:** 2026-07-27
**Aceite formal, com hash e commit:** `docs/fase-0/aceite-metodo-v1.0.md`
**Autor:** agente. Não é o executor das medições.

> **Aprovação registrada:** *"MÉTODO v1.0 APROVADO, CONDICIONADO À CORREÇÃO DA INTERPRETAÇÃO DO
> LIMITE SUPERIOR DO p99."* A correção foi aplicada antes do congelamento e **não alterou**
> estimador, amostra, procedimento, critérios de invalidação, instrumentos nem variáveis.

> **Por que este documento existe antes de qualquer código.**
> Documento 5, Fase 0: *"o método de medição é escrito e revisado **antes da coleta**, não
> declarado depois por quem mediu"*. Medição acrescentada depois custa recoleta em aparelho
> físico — por isso a lista precisa estar fechada antes da primeira sessão de coleta.

---

## 0. Congelamento e versionamento — a regra que protege todo o resto

**O aceite congela este método.** A partir dele:

1. O estado passa a **VIGENTE**, e o aceite é registrado em documento próprio
   (`aceite-metodo-v1.0.md`) com **oito campos obrigatórios**: caminho do arquivo · versão ·
   **hash** · commit · data e hora · aprovador humano · medições abrangidas · issues e ADRs
   relacionados. É o hash que prova, depois, qual método governou cada coleta.
2. **O executor não altera o método.** Ele *propõe* alteração, que passa pelo mesmo aceite.
3. Alteração aceita gera **versão nova** (1.1, 1.2, …), com quatro campos obrigatórios: o que
   mudou · por quê · quais medições já coletadas são afetadas · se a coleta anterior continua
   válida. **Nunca sobrescrever silenciosamente.** A versão anterior permanece no histórico do
   repositório e continua sendo o método que governou as coletas feitas sob ela.
4. Toda evidência arquivada declara **sob qual versão do método foi coletada**.

### A regra contra alterar o método depois de ver o resultado

Mudança em variável, tamanho amostral, critérios de invalidação, cálculo, agrupamento ou
definição de sucesso **cria versão nova**. O efeito sobre a coleta já feita é decidido pelo
critério abaixo, e não por classificação prévia do tipo de alteração:

| Natureza da alteração | Efeito sobre a coleta anterior |
|---|---|
| **Afeta comparabilidade ou interpretação** dos dados já coletados | **Invalida** a coleta anterior **da medição afetada**. Ela é refeita sob a versão nova |
| **Meramente editorial** — correção de digitação, esclarecimento de redação que não muda o procedimento | **Não invalida** |
| **Acréscimo** de medição nova | Não afeta as anteriores; a nova nasce sob a versão nova |

> **A decisão de reaproveitamento precisa ser registrada e justificada ANTES de combinar os
> dados.** Não se junta primeiro e se justifica depois: essa ordem é a que permite escolher a
> justificativa em função do resultado da junção.

Motivo da regra: método ajustado depois de observar o resultado não é método — é justificativa.
Ela vale mesmo quando a alteração é tecnicamente melhor, e vale principalmente aí, porque é o
caso em que ela é mais tentadora. Se a melhoria compensar a recoleta, faz-se a recoleta.

**Quem verifica:** a evidência arquivada declara a versão do método; o revisor confere se a
versão declarada é anterior ao início da coleta registrado na própria evidência. Divergência é
evidência inválida.

---

## 1. Papéis

| Papel | Quem | Regra |
|---|---|---|
| **Executor** | humano identificado, nomeado por medição | O template do núcleo §9 é literal: *"Executor: (humano identificado; **o agente não executa teste físico e não preenche resultado observado**)"*. Não é interpretação — é o campo do formulário |
| **Agente** | prepara tudo o que antecede a coleta | Método, instrumento de `spike/`, testes automatizáveis, entregáveis de mesa, propostas de ADR. **Não preenche resultado observado** |
| **Revisor e aprovador** | fundador | Aceita este documento antes da coleta e cada versão posterior. É o que separa método de racionalização |
| **Arquivista** | executor | Registra em `docs/evidencias/` com o template do núcleo §9, e o comportamento por marca em `docs/fabricantes/<marca>.md` |

**Evidência inventada é falha crítica.** Número não observado não entra em nenhuma tabela, nem
como estimativa, nem como "provisório". Célula sem coleta fica declarada vazia.

---

## 2. Matriz de aparelhos

Dependências do Documento 5, Fase 0: **quatro ou mais** aparelhos reais — Samsung, Motorola,
Xiaomi ou Redmi —, **dois abaixo de API 33**, um no `minSdk` 30, **um em Android 16 e um em 17**.

| ID | Marca | Android / API | Papel |
|---|---|---|---|
| A | Samsung intermediário | **11 (API 30)** | `minSdk`; pré-condição de notificação sem `POST_NOTIFICATIONS`; `fullBackupContent` |
| B | Motorola intermediário | **12 ou 12L (API 31/32)** | segundo abaixo de API 33; `dataExtractionRules` já existe |
| C | Samsung, Motorola, Xiaomi ou Redmi | **16 (API 36)** | primeira versão com cota de job a partir de FGS — metade da M9 |
| D | Xiaomi ou Redmi | **17 (API 37)** | Android atual; gerenciador de tarefas mais agressivo do conjunto |

**Limitação declarada, não simulada:** com um aparelho em 16 e um em 17, a comparação "por
fabricante" da M9 fica limitada a C e D. Vai para a evidência como limitação.

### Estados de bateria

Duas condições, **nunca simultâneas no mesmo aparelho**:

1. **Normal** — sem otimização agressiva, sem economia de bateria, aplicativo sem isenção.
2. **Economia extrema** — modo de economia do fabricante ativo, na configuração de fábrica.
   O usuário nunca é instruído a desativar a proteção de bateria (Documento 2, §35); medir com
   ela ligada é medir o produto real.

---

## 3. Controles de ambiente — registrados em toda execução

Sem estes campos, dois números da mesma medição não são comparáveis, e a exigência de *"consumo
medido com método declarado e comparável"* não é atendida.

**Identificação:** `aparelho (ID)` · `marca` · `modelo` · `versão do Android` e `nível de API` ·
`build do fabricante` (One UI / HyperOS / MIUI / Realme UI, com versão)
**Estado do aparelho:** `estado de bateria` (normal / economia extrema) · `nível de carga inicial
e final` · `carregador` (conectado / desconectado) · `isenção de otimização de bateria`
(concedida / negada) · `conjunto de permissões concedidas` · `estado do canal check_in` ·
`Não Perturbe` (ligado / desligado) · `tela` (bloqueada / desbloqueada)
**Rede:** `tipo` (Wi-Fi / móvel / avião / sem sinal) · `operadora` · `alternância durante a
execução` (sim / não)
**Rastreabilidade:** `versão do método` · `versão do spike` (commit) · `candidato de temporização`
· `intervalo de check-in configurado` · `início e fim em UTC` · `executor`

---

## 4. Dimensionamento, percentis e tratamento de dados

### 4.1 Faixa de intervalo — valor provisório declarado

O intervalo é escolhido pelo usuário entre os limites do `policy_version` (Documento 2, §12.5,
termo 1), que pertencem ao **ADR-0005-A** — cujo prazo é depois desta fase. O núcleo §0 resolve:
*"onde uma regra precisar de número antes do ADR, o número é **valor provisório declarado como
tal**, com o ADR dono identificado."*

> **Faixa provisória: mínimo 15 min · padrão 30 min · máximo 60 min. Dono: ADR-0005-A.**

**Decisão registrada do fundador (2026-07-27):** o máximo **não** é reduzido de 60 para 30 min
para encurtar o calendário. *"Essa mudança deve ocorrer por decisão de produto e evidência, não
para caber artificialmente no cronograma."* O timebox absorve o custo.

Alargar o máximo depois **obriga re-medir no novo máximo** — o p99 não se extrapola.

### 4.2 Amostra

| Onde | Estatística | n mínimo |
|---|---|---|
| M1 no **máximo** da faixa (60 min) | p50, p95, **p99** | **200** |
| M1 nos demais pontos (15 e 30 min) | p50, p95 | **100** |
| M4 (cerca de proximidade) | p50, p95 | **60** travessias por aparelho |
| M9 (cota de job) | p50, p95 | **100** execuções por condição |
| M12 (latência da confirmação) | p50, p95, p99 | **100** por condição de autenticação |
| M14 (taxa de confirmação) | proporção | **150** pedidos por condição adversa |
| Descritivas (M2, M3, M6, M7) | descritivo | **5** repetições por célula, todas registradas |
| Mercado (M11, M15) | proporção | n declarado pela fonte; **percentual sem denominador é proibido** |

**Por que n = 200 e não 300 no p99:** o próprio Documento 5 declara que *"o p99 do spike não é,
por construção, o p99 do produto"* — o mecanismo de produção nasce com `Clock` injetado,
WorkManager sob as regras do módulo 10 e sujeito à cota do Android 16 —, e a **Fase 4 re-mede com
o código de produção**. O piso de 200 é o mínimo em que o p99 é uma ordem observada e não o
máximo da amostra. **Abaixo de 200 não existe p99: existe pior caso**, e chamá-lo de p99 seria
evidência inventada.

### 4.3 Cálculo dos percentis — estimador fixado

**Método: ordem mais próxima (nearest-rank), sem interpolação.**

> p_k = valor de ordem **⌈k · n / 100⌉** na amostra ordenada crescente.

| n | p50 | p95 | p99 |
|---|---|---|---|
| 200 | ordem 100 | ordem 190 | ordem 198 |
| 100 | ordem 50 | ordem 95 | ordem 99 |

**Natureza desta escolha.** Não é uma regra estatística universal, e não se afirma que a
interpolação seja incorreta. É uma **decisão metodológica deste projeto**, motivada por
auditabilidade e rastreabilidade da evidência: o valor reportado corresponde a uma observação
real, é simples de auditar, evita diferenças silenciosas entre ferramentas, é reproduzível por
qualquer revisor e é conservador para uma prova de viabilidade operacional. A diferença entre os
dois estimadores é, de todo modo, irrelevante diante da largura do intervalo de confiança do §4.4.

> ⚠️ **Consequência operacional que o instrumento e o revisor precisam respeitar.**
> Com n = 200, o p99 pontual **é a 198ª observação ordenada** — nada mais.
> Planilhas e bibliotecas aplicam interpolação **por padrão** e produziriam outro número sem
> avisar: `PERCENTIL`/`PERCENTILE` no LibreOffice e no Excel, `numpy.percentile` e
> `numpy.quantile`, `pandas.Series.quantile` e o `quantile()` de R (tipo 7) todos interpolam por
> padrão. O cálculo desta fase é **ordenar e indexar**, e o instrumento reporta junto do valor a
> **ordem** de onde ele saiu, para que o revisor confira sem refazer a conta.

### 4.4 Intervalo de confiança — obrigatório ao lado de todo percentil

**Método: intervalo livre de distribuição por estatística de ordem (binomial exato), 95%.**

| n | Percentil | Estimativa | IC 95% (ordens) |
|---|---|---|---|
| 200 | p50 | ordem 100 | [86, 115] |
| 200 | p95 | ordem 190 | [184, 197] |
| 200 | **p99** | ordem 198 | **[195, 200]** |
| 100 | p50 | ordem 50 | [40, 61] |
| 100 | p95 | ordem 95 | [90, 100] |

> ⚠️ **Leitura obrigatória do p99 com n = 200.** O limite superior do intervalo cai na **ordem
> 200**, isto é, no **máximo observado**. O intervalo é **censurado à direita**: com este tamanho
> de amostra a cauda superior não é bem delimitada.
>
> **O máximo da amostra não é o teto verdadeiro do p99, nem limite superior garantido da latência
> da plataforma.** É apenas **o limite observável que a amostra conseguiu produzir**. A plataforma
> pode apresentar, em uso futuro, atrasos maiores que o máximo medido.

**Como isso entra no ADR-0005-B, redigido no escopo exato do que os dados sustentam:**

> A proposta inicial de `margem_de_rede` será derivada de forma conservadora a partir do **limite
> superior observável do intervalo amostral do p99**, que, quando censurado no tamanho de amostra
> adotado, **coincide com o máximo observado**. Esse valor **não constitui teto estatístico
> garantido da plataforma** e deverá receber **fator ou parcela adicional de segurança**, definida
> no ADR-0005-B.

A distinção não é preciosismo de redação: sem ela, o ADR trataria o máximo observado como uma
garantia estatística que os dados não fornecem — e `margem_de_rede` subdimensionada produz falso
positivo, que é o erro caro deste produto.

### 4.5 Tratamento de outliers

> **Não existe descarte de amostra individual. Nenhum. Em nenhuma medição.**

Motivo: em medição de latência de agendamento, **o outlier é o fenômeno**. O p99 existe
precisamente para capturar a cauda que o Android produz em Doze, em economia extrema e sob
gerenciador de fabricante. Remover a cauda de uma medição de cauda destrói a medição — e o número
sobrevivente pareceria melhor, o que é exatamente a direção do erro que este projeto não pode
cometer.

O que existe é **invalidação de execução** (§5), com causa registrada, e ela invalida a
**execução inteira**, não a amostra inconveniente dentro dela.

**Regra de blindagem:** os critérios de invalidação do §5 são fechados por este documento
**antes** da coleta e **não podem ser aplicados retroativamente** a uma execução por causa do
valor observado. Invalidar uma execução exige a causa registrada no momento em que ela ocorreu,
nos controles de ambiente do §3 — não depois, ao olhar o resultado.

**Amostra perdida por falha do instrumento** (o spike não gravou) é registrada como lacuna, com o
número de amostras perdidas, e conta contra o n. Não é outlier e não é invalidação.

### A execução invalidada não desaparece

> **Uma execução invalidada permanece no conjunto de auditoria; sai apenas do conjunto
> estatístico principal.**

O relatório de cada medição declara, obrigatoriamente: **quantas execuções foram invalidadas ·
por qual motivo · em qual aparelho e configuração**.

**Uma taxa elevada de invalidação é, ela própria, um resultado.** Pode indicar instrumento
frágil, procedimento pouco reproduzível ou plataforma instável — e qualquer das três muda a
leitura do número que sobreviveu. Uma medição cujo p99 é bom, obtida descartando metade das
execuções, não é uma medição boa.

---

## 5. Critérios de invalidação de execução

Uma execução é inválida — e **refeita inteira** — quando qualquer destes ocorreu, registrado no
momento:

**Gerais, valem para toda medição**

- interação do usuário com o aparelho durante a janela, fora do previsto pelo procedimento;
- carregador conectado em medição que o proíbe;
- reboot, atualização de sistema ou de aplicativo não planejados;
- alteração de qualquer controle do §3 no meio da execução (permissão, canal, estado de bateria,
  Não Perturbe, isenção);
- versão do spike diferente da declarada no início;
- n final abaixo do mínimo do §4.2;
- ausência de qualquer campo obrigatório do §3.

**Específicos**

| Medição | Invalida também |
|---|---|
| M1, M9, M12 | alternância de rede não registrada; relógio do sistema ajustado durante a janela |
| M5, M8, M13 | tela ligada; carga inicial fora da faixa declarada; mudança de rede no meio |
| M4 | travessia sem confirmação independente do instante; raio da cerca diferente do declarado |
| M6, M14 | ensaio em build **não instalado pela Play** (ver ARB4-014, issue #14) |
| M10 | número de teste fora da operadora declarada; texto diferente do texto real previsto |
| M2, M3, M7 | conjunto de permissões diferente do declarado para a célula |

---

## 6. As quinze medições

Cada linha declara **hipótese testada**, **variável medida** e **o que constitui reprovação**.

> **Sobre "sucesso e reprovação":** há dois tipos, e a distinção é obrigatória.
> **Reprovação estrutural** é definida pelos documentos, é binária e não depende de número — ela
> determina se a hipótese técnica sobrevive, e por isso pertence ao método.
> **Reprovação por limiar numérico** de bateria e de falso positivo é
> **`[ABERTO — FASE 0]`, propriedade do ADR-0012**, e **não é declarada aqui**: o método define
> *como medir*; o ADR define *como interpretar e aceitar*. Declarar limiar neste documento
> fecharia item aberto sem ADR.

### 6.0 Reprovação estrutural — as seis formas de a hipótese técnica não sobreviver

Qualquer uma reprova a fase, independentemente de número:

1. **Nenhum candidato** de temporização oferece comportamento compatível com a promessa;
2. **a retomada é inviável na matriz mínima** — nem por boot, nem por qualquer gatilho;
3. **a política da Play elimina todos os candidatos necessários** — reprovação por política, não
   por medição, e é por isso que o parecer de monitoramento vem antes do ADR-0007;
4. **o canal de alerta não possui viabilidade operacional** — inclusive por filtragem de SMS com
   link sem alternativa praticável;
5. **o consumo impede sessão de duração útil** — não é o limiar fino do ADR-0012, é a
   impossibilidade de o produto existir num dia de uso;
6. **o instrumento não consegue produzir evidência reproduzível** — se a mesma configuração
   produz resultados que não se repetem, não há o que interpretar, e o problema é anterior a
   qualquer limiar.

A sexta é a que se costuma esquecer, e conecta-se ao §4.5: **taxa elevada de invalidação é
sintoma dela**.

| # | Hipótese testada | Variável medida | Saída | Reprovação estrutural |
|---|---|---|---|---|
| **M1** | *"O Android permite uma temporização suficientemente confiável"* (Doc 1, §24.1, hip. 9) | Intervalo entre o vencimento do prazo (relógio monotônico) e a **postagem** da notificação pelo sistema | p50, p95, **p99** com IC, por fabricante × bateria × ponto de intervalo × candidato | **Nenhum** candidato entregar atraso compatível com a promessa → interromper ou redesenhar |
| **M2** | A sessão é retomada após reinício sem intervenção do usuário | Retomada ocorreu? Em quanto tempo? | `possível` / `parcial` / `não comprovada`, por conjunto de permissões e fabricante | Retomada impossível em toda a matriz, para todos os candidatos |
| **M3** | A sessão é retomada após force-stop e após o gerenciador de tarefas do fabricante | Idem, e qual gatilho reativa | Idem, com o gatilho identificado | — (retomada impossível gera aviso honesto ao usuário e evento ao servidor, não reprova a fase) |
| **M4** | A cerca de proximidade tem latência utilizável e sobrevive ao reinício | Intervalo entre a travessia física e a entrega do evento | p50, p95 com IC, por fabricante; e sobrevivência a reboot | — (a cerca é "desejável no MVP", Doc 1 §21.2; latência inviável a remove do escopo, não interrompe a fase) |
| **M5** | *"O produto opera com consumo de bateria aceitável"* (hip. 5) | % de carga consumida por hora em sessão de nível `economico` | % por hora **e** % do ciclo diário, com o método do §6.1 citado por extenso | Consumo incompatível com uso diário → interromper ou redesenhar. **O limiar é do ADR-0012** |
| **M6** | A notificação é entregue e vista com tela bloqueada e Não Perturbe | O que aparece, soa e vibra, por canal | Descritivo por fabricante, com captura de conteúdo **sintético** | A notificação não chegar com tela bloqueada em toda a matriz |
| **M7** | A invalidação de chave é detectável e nunca falha em silêncio | Por chave × evento: sobrevive / é invalidada / é detectável / emite `local_key_invalidated` | Tabela por chave, evento e fabricante | Invalidação **não detectável** — o sistema não distinguiria "sem eventos" de "eventos ilegíveis" |
| **M8** | O heartbeat distingue "app vivo" de "aparelho inalcançável" a custo aceitável | Δ de consumo com e sem heartbeat; segundos até a detecção ser possível | % por hora nas duas condições; segundos até detecção | — (alimenta a cadência do ADR-0005-A) |
| **M9** | A cota de jobs do Android 16 não inviabiliza o trabalho diferível a partir do FGS | Atraso entre agendamento e execução do worker, com e sem FGS ativo | p50, p95 com IC, em Android 16 e 17 | Trabalho diferível inexecutável a partir do FGS → elimina o candidato correspondente no ADR-0007 |
| **M10** | *"Existe canal capaz de alertar o contato com urgência a custo viável"* (hip. 11) | Entrega (sim/não) e tempo até entrega de SMS **com link curto**, por operadora × provedor | Matriz operadora × provedor, com taxa e latência | **Não existir canal viável a custo aceitável, inclusive por filtragem de link sem alternativa praticável** → interromper (Doc 1, §32) |
| **M11** | — (levantamento; a hipótese pertence à decisão pendente) | Fração do público-alvo **sem** bloqueio de tela | % com **n e fonte declarados** | — (insumo da decisão pendente do Doc 2 §14.3, issue #6) |
| **M12** | A confirmação assinada é concluível em tempo compatível com uso de rua | Intervalo entre a abertura do prompt e a assinatura verificada pela chave pública local | p50, p95, p99 com IC, por fabricante × condição (biometria / credencial) | — (insumo do ADR-0012 e do orçamento do §12.5) |
| **M13** | O custo de bateria de N confirmações por sessão é aceitável | % de carga por confirmação e por sessão | % por confirmação e por sessão, por fabricante | Ver M5. **Limiar do ADR-0012** |
| **M14** | A autenticação a cada confirmação é praticável na rua sem falso positivo intolerável | Fração de pedidos respondidos dentro de `intervalo + graça`, sob condição adversa | Taxa com **n declarado**, por condição, **com motivo de falha registrado** | Taxa incompatível com a promessa → o falso positivo **externo** é o único que bloqueia release. **Limiar do ADR-0012** |
| **M15** | O `minSdk` 30 se justifica pela distribuição real do público-alvo | Distribuição de versões do Android no público-alvo | % por faixa de API, com **n e fonte**, destacando a fração abaixo de API 33 | — (fecha o 2º critério do ADR-0002) |

### 6.1 Procedimentos que precisam de detalhe

**M1** — sessão contínua no intervalo declarado; sem interação; tela bloqueada; **sem carregador**
(o carregador altera o regime de economia e invalidaria a célula). Roda para **cada candidato
sobrevivente** do Documento 2 §12.2 — e a lista de sobreviventes depende do parecer de
monitoramento (§8). O instrumento grava `(prazo_previsto_elapsed, prazo_real_elapsed, boot_id,
candidato, controles do §3)`, apenas com dados sintéticos.

**M5, M8, M13 — método de consumo, declarado por extenso porque é o que o torna comparável.**
Janela de duração fixa, **mínimo 4 h**, tela desligada, sem interação, sem carregador, partindo de
um nível inicial de carga declarado. Duas colunas na evidência, e a segunda **não substitui** a
primeira: (a) diferença de percentual de carga do aparelho na janela; (b) consumo atribuído ao
aplicativo pelo próprio sistema. Divergência grande entre as duas é resultado, não erro.

**M7** — cobre `K_dados`, `K_leitura` e `K_confirmacao` contra três eventos: novo cadastro
biométrico; restauração do aparelho; remoção do bloqueio de tela. **Inclui chave que aceita
`DEVICE_CREDENTIAL`** — é o `VERIFICAR:` aberto do Documento 2 §14.3. Produz também o fato que a
issue #3 (ARB4-009) precisa para virar regra.

**M10** — trabalho de mesa, não usa `spike/`. Para **cada** das três cotações, enviar o **texto
real previsto**, com link curto, para números das três operadoras, em pelo menos três janelas de
horário distintas. Registrar entrega, não-entrega e latência **por operadora e por provedor**.
Se houver filtragem, o **plano B de affordance é escrito antes de contratar**, não depois.

**M14 — limite honesto da medição, declarado na evidência.** O executor escreveu o produto e sabe
que está sendo medido. **O número tem viés otimista por construção.** O valor real só aparece no
Beta A. A evidência declara isso; o ADR-0012 usa o número sabendo disso.

---

## 7. Resultados que exigem ADR

| Resultado | ADR | Efeito |
|---|---|---|
| M1 + M2 + M3 + M5 + M9, por candidato | **ADR-0007** | Escolhe o mecanismo de temporização. **Precedido pelo parecer de monitoramento**, que pode eliminar candidato por política |
| p99 da M1 no máximo da faixa, **limite superior do IC** | **ADR-0005-B** | Fecha a `margem_de_rede`. Item `[ABERTO — FASE 0]` |
| M5, M8, M13, M14 | **ADR-0012** | Fecha os limiares de bateria e de falso positivo. Item `[ABERTO — FASE 0]` |
| M10 + cotações | **ADR-0011 provisório** | Provedor de SMS e cascata; definitivo antes da Fase 3 |
| Verificação do formulário real no Play Console | **ADR-0008** | Recurso principal declarado de localização em segundo plano. Item `[ABERTO — FASE 0]` |
| M8 (cadência), M12 (latência) | **ADR-0005-A** | Confirma ou revisa os valores provisórios |
| M15 | **revisão do ADR-0002** | Fecha o 2º critério do `minSdk` |
| M7 | insumo da issue #3 | Comportamento de `K_confirmacao` invalidada com sessão ativa |
| M11 | insumo da issue #6 | Decisão pendente da tela de bloqueio segura |

**Nenhum item `[ABERTO — FASE 0]` é fechado por este documento.** Ele produz o número; o ADR
fecha o item, e só o fundador aceita ADR.

### 7.1 O que o ADR-0005-B precisa decidir, e o que este método não decide

A `margem_de_rede` **não é** o p99 observado. É:

```text
margem_de_rede = referência conservadora observada + parcela operacional de segurança
```

A **referência conservadora observada** vem desta fase: o limite superior observável do intervalo
amostral do p99 (§4.4), com a ressalva de que ele **não é teto estatístico garantido**.

A **parcela operacional de segurança não é inventada neste método.** Ela é decidida no ADR-0005-B,
considerando, no mínimo: distribuição observada · diferença entre fabricantes · diferença entre
estados de bateria · comportamento em Doze · tamanho da amostra · impacto do atraso na
experiência · custo do falso positivo · capacidade de reconciliação do servidor.

**O ADR-0005-B distingue quatro margens, e elas não precisam ser iguais:**

| Margem | Onde atua |
|---|---|
| **Margem do aparelho** | quanto o aparelho tolera antes de registrar a ausência |
| **Margem do vigilante** | quanto o servidor espera antes de avaliar o prazo vencido |
| **Período de graça apresentado ao usuário** | o que a interface promete e o usuário percebe |
| **Margem adicional com cobertura degradada** | eventual acréscimo em `COBERTURA_SUSPENSA` |

Tratá-las como um único número é o erro que o §12.5 já evita ao separar os termos do orçamento.

### 7.2 Dimensões pré-registradas do ADR-0012

Os limiares de bateria e de falso positivo **não são fixados aqui**, e o ADR-0012 precisa ser
decidido **antes do encerramento da Fase 0** — não antes do começo da coleta.

Para que o ADR não seja ajustado aos resultados escolhendo só as métricas favoráveis, **as
dimensões que ele vai considerar ficam registradas antes da coleta**:

| Dimensão | Medição que a produz | Cobertura |
|---|---|---|
| Consumo adicional por hora | M5, M8, M13 | ✅ |
| Consumo adicional no ciclo diário | M5 | ✅ |
| **Consumo por nível de coleta** (`economico`, `elevado`, `emergencial`) | M5 | ⚠️ **parcial — ver nota** |
| Impacto por fabricante | M5, M8, M13, por célula | ✅ |
| Impacto em tela bloqueada | M5, M6 | ✅ |
| Taxa de escalonamento interno indevido | M14 | ✅ |
| Taxa de acionamento externo indevido | M14 | ✅ |
| Taxa de check-in não percebido | M14, motivo de falha "não viu" | ✅ |
| Duração típica de sessão | controles do §3 | ✅ |
| Custo operacional do falso positivo | planilha de custo (entregável de mesa) + M10 | ✅ |

> ⚠️ **Lacuna de cobertura declarada, não corrigida aqui.** O Documento 5 manda medir o consumo
> **apenas no nível `economico`**. Os níveis `elevado` e `emergencial` (Documento 2, §13.1) não
> são medidos nesta fase como o escopo está escrito. Corrigir isso seria **alterar o escopo do
> Documento 5**, o que a aprovação deste método explicitamente não autoriza. Antes do ADR-0012,
> uma das duas: acrescentar os dois níveis por versão nova do método — o que é **acréscimo**, e
> pelo §0 não invalida nada já coletado —, ou o ADR-0012 declarar a dimensão como **não medida**
> e decidir sem ela. Registrado como pendência da fase, não como decisão tomada.

---

## 8. Ordem de execução

A ordem não é preferência: há uma dependência declarada que a inverte.

```text
1. Parecer de classificação como aplicativo de monitoramento          ← mesa
   │  Pode eliminar candidato de temporização POR POLÍTICA: a política exige
   │  notificação persistente durante toda a execução, mais ícone único.
   │  Se o produto se enquadrar, "WorkManager puro" cai antes de qualquer
   │  medição — e medi-lo seria desperdício de calendário.
   ▼
2. Lista final de candidatos  →  define o volume real da M1
   ▼
3. M10 + M11 + M15 (mesa e pesquisa)   ‖  em paralelo, sem hardware
   ▼
4. M2, M3, M6, M7 (ensaios curtos)  →  eliminam candidatos cedo
   ▼
5. M1, M5, M8, M9, M12, M13 (coleta longa)  →  dominam o calendário
   ▼
6. M4, M14 (campo, exigem deslocamento)
   ▼
7. ADRs propostos: 0007, 0008, 0011-provisório, 0012, 0005-B
```

---

## 9. Registro e arquivamento

- Cada medição gera registro no **template do núcleo §9** (`docs/agentes/00-nucleo.md`), colado
  na PR e arquivado em `docs/evidencias/`, com **a versão deste método declarada**.
- Comportamento divergente por marca vai para `docs/fabricantes/<marca>.md`, com aparelho e
  versão testados.
- Dados brutos: apenas sintéticos. Nenhum dado pessoal real, nenhuma coordenada real de pessoa
  real, nem em captura de tela (módulo 40, §2).
- Toda saída percentual declara **n**. **Percentual sem denominador é proibido.**
- Todo percentil vem acompanhado do **intervalo de confiança** do §4.4.

---

## 10. Condições de interrupção que estas medições podem acionar

Documento 5, Fase 0, e Documento 1, §32. Se qualquer uma se confirmar, a fase **para e
redesenha**, em vez de seguir para a Fase 1:

- a solução depender de Accessibility ou de comportamento oculto;
- **nenhum** candidato de temporização entregar atraso compatível com a promessa (M1);
- o consumo for incompatível com uso diário (M5, M8, M13);
- o recurso principal de localização em segundo plano não tiver caminho de aprovação;
- **não existir canal de alerta viável a custo aceitável — inclusive por filtragem de SMS com
  link sem alternativa praticável** (M10).
