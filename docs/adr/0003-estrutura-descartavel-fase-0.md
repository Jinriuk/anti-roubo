# ADR-0003 — Estrutura descartável da Fase 0

**Estado:** PROPOSTO — aguarda aceite do fundador
**Data da proposta:** 2026-07-26
**Prazo declarado:** antes da Fase 0 (Documento 2, §39)
**Autor:** agente. Agente propõe; só o fundador aceita.

---

## Contexto

A Fase 0 existe para medir as hipóteses de plataforma e de loja que podem inviabilizar o
produto. **Todo código dela é descartável por definição**: vive em `spike/`, fora da árvore de
produção, e reuso exige reimplementação sob as regras do Documento 4 (Documento 5, Fase 0;
módulo 50, §1).

A distinção que este ADR precisa tornar operacional é a seguinte, e ela é a razão de o ADR
existir:

> **A isenção vale para o código, não para a evidência.** A Fase 0 é isenta das regras de
> código do Documento 4 — sem pisos de cobertura, sem expand-contract, sem PR pequena — **sob a
> condição de que nada dela vá a produção**. Não é isenta das regras de evidência, porque nesta
> fase a evidência **é** o entregável: o p99 medido vira `margem_de_rede` em produção, e os
> limiares de bateria viram gate de release por ADR-0012.

Sem uma fronteira mecânica, essa condição vira disciplina — e o Documento 4 existe justamente
porque disciplina com aparência de automação foi o defeito que a auditoria nomeou mais de uma
vez (ARB2-028, ARB3-017).

Há um risco concreto e específico: o CI aplica o rótulo `aguardando-evidencia-campo`
automaticamente quando o diff toca `AndroidManifest.xml`, declarações de serviço em primeiro
plano ou arquivos de política de backup (módulo 50, §3). **Um spike de temporização tem os
três**, por construção. Sem exclusão explícita, todo commit de spike entra na fila de evidência
de campo de produção e o rótulo perde sentido no momento em que mais importa.

## Opções consideradas

| Opção | A favor | Contra |
|---|---|---|
| **`spike/` no mesmo repositório** (escolhida) | Rastreabilidade: o ADR-0007 e o ADR-0008 citam medições cujo código precisa ser localizável. Mesma issue tracker, mesmo secrets scanning, mesma regra de dado sintético. As evidências (`docs/evidencias/`, `docs/fabricantes/`) já vivem aqui e sobrevivem à fase | O código fica permanentemente ao alcance de um `import`. O controle contra promoção precisa ser mecânico, não cultural |
| Repositório separado | Elimina por construção o risco de promoção acidental | Duplica contexto, credenciais e CI; e separa o código da medição das evidências que ele produziu, que é o que precisa ser auditável depois |
| Branch descartável, nunca mergeada | Custo zero de estrutura | O código desaparece com a branch. O ADR-0007 passaria a citar uma medição cujo instrumento não existe mais. Contraria "sem evidência, não há aceite" |

## Decisão

### 1. Localização e forma

- Diretório **`spike/`** na raiz do repositório, um subdiretório por hipótese medida:

```text
spike/
├── README.md                 estado da fase, executor, período, aviso de descartabilidade
├── temporizacao/             candidatos do Documento 2, §12.2; atraso de disparo; reboot;
│                             force-stop; cota de job com e sem FGS em Android 16 e 17
├── cercas/                   latência de transição de cerca de proximidade; comportamento pós-reboot
├── chaves/                   invalidação por novo cadastro biométrico, restauração e remoção de
│                             bloqueio; chave que aceita DEVICE_CREDENTIAL; uma auth = uma assinatura
├── notificacao/              Não Perturbe, tela bloqueada, canal desativado em sessão
├── bateria/                  consumo por nível, com e sem heartbeat
└── persistencia/             sessão, prazo nas três bases de tempo, evento offline, sync posterior
```

- O **canal de alerta** (cotações de SMS, entrega por operadora, custo por cadastro/alerta/falso
  positivo) **não é código** e não vive em `spike/`: é trabalho de mesa, e seus artefatos vão
  para `docs/fase-0/`.

- Branches: `spike/<assunto>`, conforme o padrão de nomes do módulo 50, §1.

### 2. Fronteira mecânica contra promoção

- `spike/` **não é declarado em `settings.gradle`** dos módulos de produção. Nenhum módulo de
  `android/` ou `backend/` pode declarar dependência dele — não por regra, por ausência de alvo.
- Cada arquivo de código em `spike/` carrega **cabeçalho obrigatório**:

```kotlin
// SPIKE — FASE 0. DESCARTÁVEL. NÃO PROMOVER.
// Este código não segue as regras do Documento 4 e não vai a produção.
// Reuso exige reimplementação sob as regras do módulo aplicável. ADR-0003.
```

- O critério de aceite da Fase 0 — "nenhum arquivo promovido para a árvore de produção" — é
  verificado pela **ausência de `spike/` no grafo de dependências de produção**, não por
  inspeção visual.

### 3. Gates: o que não vale e o que continua valendo

**Não valem em `spike/`** (é a isenção declarada pelo Documento 5, Fase 0):

pisos de cobertura do módulo 40 §3 · expand-contract · limite de tamanho de PR · checklists de
PR dos módulos 10 e 20 · `TODO` com issue obrigatória · Detekt e ktlint com zero warnings ·
regras de dependência entre módulos · **aplicação do rótulo `aguardando-evidencia-campo`**
(o CI exclui os caminhos sob `spike/` do casamento de caminhos do módulo 50, §3).

**Continuam valendo, sem exceção** — porque não são regras de código, são regras de segurança
e de fornecedor:

- **secrets scanning**; nenhum segredo em código, commit, imagem ou log (módulo 30, §1);
- **nenhum dado de produção e nenhum dado pessoal real**, em nenhuma forma, inclusive em
  fixture, captura de tela ou anexo de evidência (Documento 3, §39.3; módulo 30, §9;
  módulo 40, §2);
- **nenhuma credencial de produção** acessível ao ambiente do spike;
- **`VERIFICAR:` é legítimo** em `spike/` e em documentação — o gate que o reprova é de código
  de produção;
- **regras de evidência integrais**: método escrito e revisado **antes** da coleta, template do
  núcleo §9 preenchido por executor humano identificado, resultado arquivado em
  `docs/evidencias/` e comportamento por marca em `docs/fabricantes/<marca>.md`.

### 4. Fim de vida

Ao encerrar a Fase 0, `spike/` **permanece no repositório** como registro do instrumento de
medição, com `spike/README.md` marcado `ARQUIVADO — Fase 0 encerrada em <data>`. Não é apagado:
o ADR-0007, o ADR-0008 e o ADR-0012 citam medições cujo instrumento precisa ser localizável.
O critério de aceite verificado é a **ausência de promoção**, não a ausência do diretório.

## Consequências positivas

- A condição que sustenta a isenção ("nada vai a produção") passa a ser verificável no grafo de
  build, e não uma promessa na descrição da PR.
- O rótulo de evidência de campo continua significando o que significa, em vez de ser
  disparado por todo commit de spike.
- O instrumento de medição sobrevive ao encerramento da fase, junto com a evidência que
  produziu — que é o que torna o ADR-0007 auditável depois.
- As três regras que protegem dados de pessoas reais atravessam a isenção intactas.

## Consequências negativas

- `spike/` no mesmo repositório é convite permanente ao copiar-e-colar. O cabeçalho por arquivo
  e a ausência no `settings.gradle` reduzem o risco; não o eliminam. Um agente futuro pode ler
  código de spike e reproduzir seu padrão em produção sem importar nada — e nenhum gate pega isso.
- Código sem cobertura, sem lint e sem revisão fica no repositório indefinidamente, aumentando
  o ruído para quem lê o projeto pela primeira vez.
- A exclusão de `spike/` do rótulo de evidência é uma exceção configurada no CI: exceção de
  gate é exatamente o que o módulo 40, §7 diz que só se faz com ADR — este é o ADR, e a exceção
  precisa ficar visível na configuração, não escondida num glob.

## Evidências

Documento 5, Fase 0 (natureza, escopo, critérios de aceite, "nenhum arquivo promovido").
Módulo 50, §1 (branches, `spike/`, isenção limitada ao código) e §3 (caminhos que disparam o
rótulo). Módulo 40, §2 (dado sintético), §3 (exceção de cobertura para spike descartável), §5
(medições da Fase 0), §7 (nenhum gate desativado sem ADR). Módulo 30, §1 e §9. Documento 3,
§39.1 e §39.3. Documento 2, §8 e §39.

## Data de revisão

**Encerramento da Fase 0**, junto com o relatório de encerramento da fase.
