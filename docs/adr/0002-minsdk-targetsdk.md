# ADR-0002 — `minSdk` 30 provisório e `targetSdk` 36

**Estado:** **ACEITO** pelo fundador em 2026-07-27
**Data da proposta:** 2026-07-26
**Imutável a partir do aceite.** Mudança de decisão é ADR novo que referencia e substitui este (núcleo §10).
**Prazo declarado:** antes da Fase 0 (Documento 2, §39)
**Autor:** agente. Agente propõe; só o fundador aceita.

> ⚠️ **Este ADR foi aceito com um dos dois critérios do Documento 2 §39 satisfeito.**
> O §39 determina que a decisão seja tomada "por dados de mercado **e** por custo da faixa de
> ramificação". O custo está integralmente levantado abaixo. O dado de mercado brasileiro **não
> existia** e a lacuna foi endereçada no aceite: a **distribuição de versões do Android no
> público-alvo** entrou na tabela de medições obrigatórias da Fase 0 (Documento 5), na mesma
> coleta da parcela sem tela de bloqueio segura. O critério fecha **antes** da revisão, não neste
> ADR. Ver §Lacuna.

---

## Contexto

**`targetSdk` não é escolha, é prazo.** Novos aplicativos e atualizações precisam alvejar
Android 16 (API 36) a partir de **31 de agosto de 2026**. O produto alveja 36 desde o primeiro
release, o que significa que as restrições comportamentais do Android 15 e 16 valem desde a
primeira linha. O prazo de alvo seguinte (API 37) cai por volta de **31 de agosto de 2027**,
dentro do horizonte da Fase 7 (Documento 2, §35).

**`minSdk` é escolha, e é a que tem custo.** A faixa 30 → 36 são seis níveis de API, e a
ramificação cai justamente no que é mais difícil de testar.

### Custo da faixa `minSdk` 30, ponto a ponto

| Ponto de ramificação | Nível | Efeito prático |
|---|---|---|
| `POST_NOTIFICATIONS` | só existe em **33+** | Em 30–32 a pré-condição de sessão é avaliada por `areNotificationsEnabled()` mais a importância do canal `check_in`. São **dois caminhos** para a pré-condição mais crítica do produto (módulo 10, §9) |
| Atributo de exclusão de backup | muda em **31** | Exige os **dois** mecanismos: `android:fullBackupContent` (≤ 30) **e** `android:dataExtractionRules` (≥ 31) com `<cloud-backup>` **e** `<device-transfer>`. Lista de exclusões idêntica nos dois arquivos, verificada por lint (Documento 2, §4.7) |
| Tipos e restrições de serviço em primeiro plano | mudam em **31, 34, 35, 36** | O candidato de temporização do ADR-0007 precisa se comportar em toda a faixa |
| Alarmes exatos | mudam em **31, 33, 34** | `SCHEDULE_EXACT_ALARM` negada por padrão em instalações novas, revogável pelo usuário **e pelo sistema**, com a revogação cancelando alarmes já agendados |

**Consequência direta de teste:** a matriz mínima exige **dois** aparelhos abaixo de API 33 —
um deles no `minSdk` —, porque um só não caracteriza três gerações de sistema (módulo 40, §5;
Documento 5, Fase 0).

### Linha de base de plataforma

Android **15, 16 e 17**. O Android 17 (API 37) está estável desde 16/06/2026 — a existência da
versão está confirmada em fonte oficial; a data exata da estabilização vem de fontes
secundárias e está declarada como tal pela auditoria ARB4. "Android atual" na matriz de
aparelhos significa **17**.

## Opções consideradas

| Opção | A favor | Contra |
|---|---|---|
| **`minSdk` 30** (escolhida, provisória) | Maior alcance no mercado brasileiro, que é o único mercado do MVP e onde a base de aparelhos antigos é relevante. Coerente com o produto: o público-alvo inclui quem trabalha na rua, e não apenas quem tem aparelho premium | Assume os quatro pontos de ramificação acima. Exige dois aparelhos abaixo de API 33 na matriz. Duplica o caminho da pré-condição de notificação — que é pré-condição de **sessão**, não de tela |
| `minSdk` 31 | Elimina a ramificação de backup: `dataExtractionRules` passa a ser o único mecanismo, e o lint de manifesto fica simples | Mantém a ramificação de `POST_NOTIFICATIONS` (a mais cara das quatro) e corta aparelhos em Android 11 sem resolver o problema principal |
| `minSdk` 33 | Elimina **as duas** ramificações mais caras: `POST_NOTIFICATIONS` passa a existir sempre, e o backup fica num só mecanismo. Reduz a matriz mínima e o custo de teste de forma mensurável | Corta Android 11, 12 e 12L. **Qual fração do público-alvo isso representa é exatamente o dado que falta.** Sem ele, a escolha seria por conveniência de engenharia, que é a inversão da prioridade do projeto |

## Decisão

- **`minSdk` 30, declarado provisório.**
- **`targetSdk` 36**, registrado como consequência de prazo de loja e não como escolha.
- **Linha de base de comportamento: Android 15, 16 e 17.** "Android atual" na matriz = 17.
- O agente **não altera** esses valores por conta própria (módulo 10, §11).
- A revisão ocorre após a Fase 0, com o dado de mercado em mãos, e é ADR novo — este é imutável
  depois de aceito.

## Lacuna declarada — o critério que não está satisfeito

O Documento 2, §39 exige **dois** critérios. Tenho um.

A distribuição de versões do Android **no público-alvo brasileiro** — não no Brasil em geral,
e não no mundo — não está levantada, e **não consta da tabela de medições obrigatórias da
Fase 0** (Documento 5). A tabela contém um dado de mercado da mesma natureza — "parcela do
público-alvo sem tela de bloqueio segura" —, o que mostra que a Fase 0 admite pesquisa de
mercado como medição; a distribuição de versão simplesmente não foi incluída.

Consequências de aceitar este ADR sem o segundo critério:

- a escolha de 30 é conservadora na direção do alcance, que é a direção segura para o produto,
  mas **é escolha por default e não por dado**;
- o custo já assumido é irreversível a partir da Fase 1: o código versionado para API < 33 será
  escrito, testado e mantido, e removê-lo depois é trabalho, não economia;
- a matriz de aparelhos da Fase 0 já exige dois aparelhos abaixo de API 33 — **a compra
  acontece antes da revisão**, e é dinheiro gasto sob uma decisão provisória.

**Recomendação de quem propõe:** aceitar `minSdk` 30 como provisório **e** acrescentar a
distribuição de versões do público-alvo à tabela de medições da Fase 0, junto com a parcela sem
tela de bloqueio segura — são a mesma coleta, na mesma amostra, com o mesmo instrumento. Isso
custa uma linha e fecha o segundo critério antes da revisão.

> ✅ **Encaminhamento no aceite (2026-07-27).** A recomendação foi adotada: a linha
> *"Distribuição de versões do Android no público-alvo — dado de mercado, com n e fonte"* consta
> da tabela de medições obrigatórias da Fase 0 e tem critério de aceite próprio. O segundo
> critério do §39 passa a ter fonte prevista, e a revisão pós-Fase 0 deste ADR ocorrerá com os
> dois critérios em mãos. **Enquanto o dado não existir, o `minSdk` 30 permanece escolha por
> default, e este parágrafo é o registro disso.**

## Consequências positivas

- Alcance máximo no único mercado do MVP, com o custo declarado em vez de descoberto.
- `targetSdk` 36 desde o início significa que nenhuma restrição do Android 15 ou 16 aparece
  como surpresa depois — inclusive a cota de jobs iniciados a partir de um FGS, que vale para
  **todo** aplicativo em Android 16+, independente do `targetSdk`.
- A matriz de dois aparelhos abaixo de API 33 força o teste da ramificação, em vez de
  documentá-la e nunca exercitá-la.

## Consequências negativas

- Quatro pontos de ramificação, mantidos e testados a cada release, por uma pessoa.
- A pré-condição de sessão — que decide se o Modo Rua **inicia** — tem dois caminhos de
  avaliação, e o caminho de API < 33 (`areNotificationsEnabled()` mais importância do canal) é
  menos preciso que a permissão explícita.
- Dois arquivos de política de backup com lista de exclusões que precisa ser **idêntica**, sob
  pena de a identidade de instalação vazar por um dos dois caminhos. O gate de lint que verifica
  isso passa a ser crítico, não cosmético.
- Custo de hardware antecipado: dois aparelhos abaixo de API 33 comprados antes da revisão do
  `minSdk`.
- Se a revisão pós-Fase 0 concluir por `minSdk` 33, todo o código versionado escrito na Fase 1
  vira remoção — e remoção em código de notificação e de backup é mudança de comportamento
  observável, que exige evidência de campo nova.

## Evidências

Documento 2, §4.7, §35, §39. Módulo 10, §9, §11. Módulo 40, §5. Documento 5, Fase 0
(dependências e medições). Relatório ARB4, Verificação 4, item 2 e ARB4-012.

Fontes oficiais citadas pelos documentos e não reverificadas nesta proposta: Play Console —
*Target API level*; Android Developers — *Behavior changes: apps targeting Android 15*;
*Changes to foreground services* (cotas de job em Android 16); *Back up user data with Auto
Backup*; *Schedule exact alarms are denied by default*.

## Data de revisão

**Encerramento da Fase 0**, obrigatoriamente — o próprio Documento 2, §35 declara o `minSdk`
"provisório, fixado por ADR-0002 antes da Fase 0 e revisto após, com base em dados de mercado
brasileiro".
