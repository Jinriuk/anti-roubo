# Documento 4 — Módulo 50: Processo

**Versão:** 2.2 | **Substitui:** módulo 50 v1.0, v2.0 e v2.1 (correções ARB2 e ARB3)
Pressupõe `00-nucleo.md`.

---

## 1. Branches

Modelo: **trunk-based com branches curtas**, não Git Flow completo.

Motivo: Git Flow foi desenhado para times grandes com releases longos. Aqui o time é o fundador mais agentes; o custo de sincronizar branches longas supera o benefício, e branch longa é onde agente de IA diverge da arquitetura sem ninguém ver. Integração frequente é controle de qualidade.

- `main` protegida: só via PR, CI verde, sem force push.
- Branch por tarefa, vida curta (ideal abaixo de 2 dias), nome: `feat/checkin-deadline`, `fix/sync-retry`, `chore/ci-cache`, `sec/redaction-lib`, `spike/temporizacao`.
- Rebase sobre `main` antes do merge; merge por squash.
- Trabalho incompleto que precisa integrar cedo entra atrás de feature flag, não vive em branch longa.
- Release por tag (`vX.Y.Z`) a partir de `main`. Hotfix: branch curta a partir da tag, merge de volta em `main`.
- **Código da Fase 0 vive em `spike/` e é descartável por definição** (Documento 5, Fase 0). Não é promovido para a árvore de produção; reuso exige reimplementação sob estas regras. A isenção da Fase 0 vale para as regras de **código**, não para as de **evidência**: as medições são o entregável e seguem o template do núcleo §9, com método escrito e revisado antes da coleta.

---

## 2. Commits

Conventional Commits, descrição em português:

```
tipo(escopo): descrição no imperativo

feat(checkin): persistir prazo com boot_id e reidratar após reboot
fix(sync): tratar 429 com backoff e jitter
sec(logs): aplicar redaction em payload de evento
feat(watchdog): abrir suspeita com janela de reconciliação
test(streetmode): cobrir transições inválidas da máquina de estados
```

Tipos: `feat`, `fix`, `sec`, `test`, `refactor`, `perf`, `docs`, `chore`, `build`, `ci`. Escopos: nomes de módulos (`streetmode`, `checkin`, `watchdog`, `sync`, `emergency`, `contacts`, `identity`, `events`, `billing`, `panel`, `privacy`, `infra`).

Commit atômico (uma mudança lógica); a mensagem explica o porquê quando não é óbvio; proibido `wip`, `ajustes`, `fix2` em `main`; breaking change de contrato marca `!` e referencia ADR.

---

## 3. Pull Request

Toda mudança entra por PR, mesmo com um único humano no projeto: a PR é onde o pacote de evidências vive e onde o fundador revisa o trabalho dos agentes.

Template obrigatório:

```markdown
## O que muda
## Por quê
## Fase do Documento 5 e item
## Nível de evidência aplicável
(unitária | campo — e por quê; ver núcleo §3.1)
## Rótulo de evidência de campo
(aplicado automaticamente pelos caminhos alterados; se removido, justificar aqui — a justificativa fica registrada)
## Como foi testado
(unit / integração / instrumentado / manual; colar o template do núcleo §9 se houver campo)
## Módulos do Doc 4 aplicáveis e checklist correspondente
## Migrations e rollback
(ou "não há")
## Itens abertos ou pendentes tocados
(nenhum `[ABERTO — FASE 0]` fechado sem ADR; nenhum `[PENDENTE — DECISÃO DO FUNDADOR]` implementado)
## Limitações e riscos
(obrigatório; "nenhum" precisa ser verdade)
## Decisões e trade-offs
(alternativas rejeitadas; ADR ou Decision Log criado?)
## Issues
(fecha #X, cria #Y para pendências)
```

Regras: PR pequena (alvo abaixo de 400 linhas de diff significativo; acima disso, dividir ou justificar); descrição honesta sobre o que **não** está pronto; gravação para mudança de UI; CI verde antes de pedir revisão.

PR de funcionalidade crítica sem evidência de campo devida fica com o rótulo `aguardando-evidencia-campo` e **não faz merge**. O rótulo é aplicado **automaticamente** pelo CI quando o diff toca `core:location`, `core:notifications`, `core:security`, `sync/workers`, `BootReceiver`, `AndroidManifest.xml`, declarações de serviço em primeiro plano ou arquivos de política de backup. Antes desta correção o rótulo dependia do julgamento do autor, confirmado pelo mesmo revisor — o que fazia do controle mais importante do Documento 4 uma sugestão, pela régua do próprio documento.

---

## 4. Code review

Revisor: fundador, ou segundo agente como pré-filtro. Áreas críticas sempre com revisão humana final: auth, crypto, localização, **vigilante**, billing, máquinas de estado, migrations.

**Checklist:**

- [ ] Faz o que a tarefa pede, e só isso? (scope creep reprovado)
- [ ] O item pertence à fase corrente do Documento 5?
- [ ] Conformidade com os módulos do Doc 4 declarados na PR
- [ ] Nível de evidência declarado corresponde à mudança? (mudança de comportamento observável exige campo)
- [ ] Rótulo de campo removido? Se sim, a justificativa é plausível e está registrada?
- [ ] Testes provam o comportamento (ler os testes antes do código)
- [ ] Casos de falha: offline, timeout, duplicata, permissão negada, prazo expirado, reboot, force-stop
- [ ] Segurança: passa mentalmente pelo módulo 30, §§1 a 6
- [ ] Nenhum item `[ABERTO — FASE 0]` foi fechado sem ADR; nenhum `[PENDENTE — DECISÃO DO FUNDADOR]` foi implementado
- [ ] **Nenhuma reenunciação nova de conteúdo dos Documentos 2 ou 3 em módulo do Doc 4** (núcleo §0); onde existir, é gerada e validada em CI
- [ ] Dependências entre módulos respeitadas; nada vazando camada
- [ ] Legibilidade: outro agente sem contexto entende em 6 meses?
- [ ] Migrations: expand-contract, rollback, lock, nenhum índice único global em tabela quente
- [ ] Logs e erros: nada da lista proibida
- [ ] "Limitações e riscos" plausível (seção vazia com diff grande é sinal de alerta)

Cultura de review: o comentário aponta o problema e o critério do documento violado; o autor corrige ou argumenta tecnicamente; "funciona assim também" não é argumento contra regra escrita.

---

## 5. ADR e Decision Log

Dois níveis, conforme núcleo §10.

**ADR completo** — obrigatório para: toda a lista do Documento 2, §39; qualquer desvio de regra dos Documentos 2, 3, 4 ou 5; nova dependência estrutural; mudança de contrato público (API, schema de evento); mudança de `minSdk` ou `targetSdk`; criação de canal de notificação; pinning; alteração de limiares de performance; **fechamento de qualquer item `[ABERTO — FASE 0]`**; **substituição de valor provisório declarado por valor definitivo**.

**Decision Log** — escolhas de baixo risco: nomes, organização de pacote, biblioteca utilitária sem superfície de segurança, limiar de interface. Registro curto na mesma pasta, sem aprovação prévia, revisado em lote semanal.

Áreas em que só existe ADR completo, sem exceção: autenticação, criptografia, localização, billing, máquinas de estado, vigilante, migrations, retenção, permissões e políticas de loja. **Segurança nunca é aprovada por silêncio** — não há prazo cujo vencimento autorize seguir sem resposta.

**Itens `[PENDENTE — DECISÃO DO FUNDADOR]`** não são resolvidos por ADR proposto por agente: são decisões de produto com custo de mercado ou de escopo. O agente apresenta opções e custos, e para. São três (núcleo §0): tela de bloqueio segura e destino do PIN interno; número de contatos no MVP; saída para o encerramento offline.

**ADRs que a terceira rodada acrescentou ou partiu:** ADR-0013 (mecanismo de prova de autenticação da confirmação) antes da Fase 1; ADR-0005 partido em 0005-A (parâmetros provisórios, antes da Fase 1) e 0005-B (parâmetros que dependem de medição, após a Fase 0), com o 0005-B como dono único da `margem_de_rede`.

**Ordem entre ADRs, quando existe dependência declarada:** o parecer de classificação como aplicativo de monitoramento precede o ADR-0007, porque pode eliminar candidato de temporização por política (Documento 2, §12.2). ADR-0011 é proposto já na Fase 0, com base no teste de canal.

Local: `docs/adr/NNNN-titulo.md`, numeração sequencial, modelo do Documento 2, §39. ADR é imutável depois de aceito; mudança de decisão é ADR novo que referencia e substitui o anterior. Agente pode **propor**; só o fundador aceita.

---

## 6. Dependências (bibliotecas)

Adicionar biblioteca exige, na PR:

- o problema que ela resolve e por que não vale implementar (20 linhas próprias valem mais que dependência nova para coisa trivial);
- licença compatível; manutenção ativa; ausência de CVE crítico aberto;
- custo: tamanho no APK ou na imagem, permissões, dependências transitivas;
- encapsulamento: SDK com superfície grande entra atrás de interface própria (módulo 10, §1).

Proibido: biblioteca abandonada; biblioteca de criptografia fora das aprovadas (módulo 30, §3); dependência que exija reduzir controles de segurança.

Versões pinadas (version catalog no Android, lockfile no backend); atualização de dependência é PR própria com changelog lido, nunca carona em PR de feature.

---

## 7. Versionamento

- App: `versionName` em SemVer, `versionCode` monotônico automatizado no CI.
- API: prefixo `/api/v1`; regra de compatibilidade no módulo 20, §2.
- Eventos: `schema_version` próprio, evolução aditiva. A inclusão de `signature` no envelope (Documento 2, §16.8) é aditiva e entra por ADR, porque muda contrato público.
- Banco: migrations numeradas sequencialmente, nunca editadas após merge.
- Documentos: cada documento carrega versão e a linha "Substitui". Alteração de documento é PR, com ADR quando muda decisão.

---

## 8. Release e rollback

Fases e rollout gradual conforme Documento 2, §34: interno, fechado, produção 1%, 5%, 20%, 50%, 100%.

- Avanço de faixa exige: crash-free e ANR dentro do limiar; sem regressão de bateria; sync saudável; `outbox_lag`, **atraso do vigilante** e **soma ponta a ponta** dentro do SLO; **falso positivo externo dentro do limiar**; nenhum bloqueador do Documento 3, §51.
- Todo release tem rollback definido **antes** de sair: app (halt de rollout e release anterior), backend (deploy anterior com compatibilidade garantida pelo expand-contract), banco (migration reversa ou plano documentado).
- Mudança arriscada nasce atrás de feature flag com kill-switch; flag é configuração de comportamento pré-definido, **nunca** canal para código ou regra arbitrária remota.
- Flags temporárias têm issue de remoção; flag morta há 2 releases é dívida a limpar.
- **Checklist de publicação na Play**, revisado a cada release que toque permissões ou comportamento de fundo:
  - [ ] matriz de permissões e políticas do Documento 2, §34.5 revisada;
  - [ ] declaração de localização em segundo plano coerente com o **recurso principal** declarado no formulário, e listagem alinhada a ele — lembrando que o produto **pode** promover um conjunto de recursos centrais, e a restrição é do formulário, não do produto;
  - [ ] **declaração de escopo mínimo de `ACCESS_FINE_LOCATION`** submetida, se o app já alvejar Android 17 ou superior (gate da Fase 7);
  - [ ] declarações de tipo de serviço em primeiro plano atualizadas, com vídeo por tipo, e **tipo compatível com o timeout aplicável e com a retomada pós-boot** (Documento 2, §35);
  - [ ] **política de aplicativos de monitoramento**: notificação persistente durante toda a execução, ícone único, divulgação na descrição, e decisão registrada sobre `isMonitoringTool`;
  - [ ] Data Safety coerente com o tratamento real;
  - [ ] página pública de exclusão de conta ativa e endpoint funcionando; fluxo de exportação e de demais direitos do titular disponível;
  - [ ] classificação de conteúdo e público-alvo coerentes com a restrição de 18 anos;
  - [ ] vídeo de demonstração atualizado.

---

## 9. Documentação viva

- Toda mudança de comportamento atualiza a documentação correspondente no mesmo PR: OpenAPI, README do módulo, `docs/fabricantes/`, runbooks, e os Documentos 1 a 5 quando a mudança os afetar (via ADR).
- Divergência entre documento e código é bug: abrir issue apontando qual dos dois está errado.
- **Módulo do Documento 4 não reenuncia conteúdo dos Documentos 2 ou 3** (núcleo §0). Onde a reenunciação for útil ao agente, ela é **gerada** do original, marcada no texto com a origem (`<!-- gerado de ... -->`) e validada em CI. Reenunciação divergente é bug, não conflito de hierarquia — foi assim que uma lista de eventos auditáveis perdeu quatro itens, incluindo rotação de chave e alteração administrativa, que são os dois que permitem detectar abuso de insider.
- **Termo abolido pelo glossário** (Documento 2, §3) encontrado em código, comentário, string ou documento vira issue.
- Referência a "Adendo A" é obsoleta: o conteúdo está no Documento 2, com a numeração de seções preservada. O Adendo não volta a existir.
- Afirmação sobre plataforma ou política de loja precisa de fonte oficial, inclusive quando é **mais restritiva** que a fonte: inventar rigor também é inventar (módulo 30, §9).
- Evidências de teste manual arquivadas em `docs/evidencias/` com data e versão.

---

## 10. Issues e TODOs

- Todo `TODO` no código: `TODO(#123): descrição`, com issue existente. **O CI verifica e falha o build** quando o padrão não é atendido.
- `VERIFICAR:` remanescente em código de produção **falha o build**. É marcador de incerteza do agente, não comentário permanente: vira issue ou é resolvido antes do merge. Em **documentação** o `VERIFICAR:` é legítimo e permanece até ser medido ou verificado com fonte oficial.
- Bug encontrado durante qualquer tarefa vira issue imediatamente, mesmo fora do escopo (núcleo §5).
- Pendência descoberta na PR vira issue linkada na seção "Limitações e riscos", nunca promessa verbal.

---

## 11. Gates

A lista de gates automatizados é a do núcleo §11. Nenhum gate é desativado, ignorado ou tornado não bloqueante sem ADR e issue de reativação com prazo.
