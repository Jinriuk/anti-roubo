# Fase 0 — Viabilidade técnica

**Estado: ✅ FASE 0 FORMALMENTE AUTORIZADA.** Timebox declarado e método congelado — ver §2.
**Autoridade da fase:** Documento 5, Fase 0. Este arquivo não define escopo, critério de aceite
nem evidência; ele registra **situação** e **pendências**.

---

## 1. O que a fase é

Medir as hipóteses de plataforma e de loja que podem inviabilizar o produto, e produzir as
declarações da Google Play.

> **O entregável real é matriz de capacidade, medições e ADRs. Não é software.**

Todo código da fase é descartável por definição, vive em `spike/` e não é promovido
(ADR-0003, aceito). A isenção vale para as regras de **código**, não para as de **evidência** —
nesta fase a evidência **é** o entregável: o p99 medido vira `margem_de_rede` em produção e os
limiares de bateria viram gate de release por ADR-0012.

**Três hipóteses críticas** são testadas aqui e em nenhuma fase posterior (Documento 1, §24.2):
viabilidade da temporização · aprovação da loja · **canal de alerta**.

## 2. Pré-condições de entrada — situação em 2026-07-27

| # | Pré-condição | Situação |
|---|---|---|
| 1 | **ADR-0001** aprovado | ✅ **ACEITO** |
| 2 | **ADR-0002** aprovado | ✅ **ACEITO** — segundo critério do §39 endereçado pela medição M15 |
| 3 | **ADR-0003** aprovado | ✅ **ACEITO** |
| 4 | Aparelhos reais: dois abaixo de API 33, um em Android 16, um em Android 17 | ✅ **disponíveis**; Documento 5 corrigido para exigir os quatro |
| 5 | **Método de medição escrito, revisado e aceito antes da coleta** | ✅ **v1.0 VIGENTE e congelado** — aceite em `aceite-metodo-v1.0.md`, com hash e commit |
| 6 | Template de evidência (núcleo §9) | ✅ disponível em `docs/agentes/00-nucleo.md` |
| 7 | **Timebox declarado** (alvo e limite duro, em semanas) — Doc 5, §2 | ✅ **6 semanas de alvo, 8 de limite duro** (Documento 5, Fase 0) |
| 8 | Servidor de teste | ⏳ a confirmar |
| 9 | Conta no Play Console | ⏳ a confirmar |
| 10 | Contas de teste em três operadoras | ⏳ a confirmar |

**Nenhuma condição trava a partida formal.** As condições #8 a #10 travam medições específicas
(M10 e a sincronização do protótipo), não a fase — os entregáveis de mesa e os ensaios de
plataforma não dependem delas, e a semana 1 do timebox é exatamente para resolvê-las.

> **A Fase 0 está autorizada a iniciar.** Timebox declarado no Documento 5; método v1.0 congelado
> com hash `e3367d84…` no commit `6b334d6e`.

## 3. Decisões tomadas em 2026-07-27

| ID | Decisão | Resultado |
|---|---|---|
| D1 | Núcleo | `00-nucleo.md` v2.3 no repositório. **A hierarquia do §0 põe o Documento 4 no nível 3, acima dos Documentos 5, 2 e 1** — `CLAUDE.md` corrigido |
| D2 | ADRs 0001, 0002 e 0003 | Aceitos, imutáveis a partir do aceite |
| D3 | Medições do ARB4-007 | Incorporadas ao Documento 5: M12, M13, M14, com critérios de aceite próprios |
| D4 | Faixa de intervalo de confirmação | **Valor provisório declarado: 15 / 30 / 60 min**, dono ADR-0005-A (núcleo §0). Máximo **mantido em 60** por decisão de 27/07 |
| D5 | Matriz de aparelhos | Lacuna do Android 16 resolvida; Documento 5 corrigido para quatro aparelhos |
| D6 | Seis itens do portão da ARB4 | Aplicados — ver `docs/auditoria/CHANGELOG-ARB4.md` |
| D7 | Distribuição de versões do Android | Incorporada como M15, fechando o segundo critério do ADR-0002 |
| D8 | Papéis | Confirmado pelo próprio template do núcleo §9: o agente prepara tudo e **não preenche resultado observado**; a coleta é de executor humano identificado |
| D9 | Issues | Backlog de consistência convertido em issues do repositório |

## 4. Timebox declarado — 6 semanas de alvo, 8 de limite duro

Registrado em `docs/05-roadmap.md`, Fase 0, em 2026-07-27.

| Período | Conteúdo |
|---|---|
| **Semana 1** | Preparação: aceite do método, instrumentos, aparelhos, contas e operadoras |
| **Semanas 2 a 5** | Coleta principal **e entregáveis de mesa em paralelo** |
| **Semana 6** | Consolidação, ADRs e relatório de encerramento |
| **Semanas 7 e 8** | **Reserva** para ensaios invalidados, dependências externas e lacunas |

**Ao atingir as 8 semanas há decisão formal**, não extensão automática: aceitar evidência
parcial · reduzir escopo · redesenhar hipótese · reprovar a fase.

**A faixa de 60 min foi mantida.** Não foi reduzida para 30 apenas para encurtar o calendário —
redução é decisão de produto com base em evidência. O timebox absorve o custo.

O termo dominante do calendário é a **M1**:

| Bloco | Calendário estimado |
|---|---|
| **M1** — atraso de disparo, três pontos de intervalo, dois estados de bateria | **~23 dias** de coleta contínua |
| M5, M8, M13 — consumo e heartbeat, em paralelo nos mesmos aparelhos | absorvido pela M1 |
| M2, M3, M6, M7 — ensaios curtos e discretos | ~3 a 5 dias |
| M9 — cota de job, Android 16 e 17 | ~2 dias |
| M4, M14 — campo, exigem deslocamento | ~3 a 5 dias |
| M10, M11, M15 — mesa e pesquisa, em paralelo | ~5 a 10 dias, sem hardware |
| Entregáveis de loja e parecer de monitoramento | ~5 dias, sem hardware |

A alavanca existe — reduzir o máximo de 60 para 30 min cortaria a M1 de ~23 para ~14 dias — e
**foi deliberadamente não acionada**. Ela pertence ao ADR-0005-A e a uma decisão de produto com
evidência, não ao cronograma.

## 5. Entregáveis de mesa — não dependem de hardware

- [x] **Parecer escrito de classificação como aplicativo de monitoramento** — `parecer-classificacao-monitoramento.md`.
      **PROVISÓRIO**, com seis verificações pendentes que exigem a conta do Play Console.
      Conclusão provisória: o produto **não é** aplicativo de monitoramento, e por isso
      **nenhum candidato de temporização é eliminado por esta política** — `WorkManager` puro
      segue vivo para o ADR-0007. Achado: enquadrar-se não custaria uma notificação, **impediria
      a publicação** (issue #20)
- [ ] Decisão sobre a flag `isMonitoringTool` e sobre a divulgação na descrição da loja
- [ ] Matriz de permissões e políticas do Documento 2, §34.5 preenchida
- [ ] Textos das declarações · roteiro do vídeo · texto de divulgação proeminente
- [ ] Rascunho de Data Safety coerente com a matriz de tratamento do Documento 3
- [ ] Rascunho da declaração de escopo mínimo de `ACCESS_FINE_LOCATION` (gate da Fase 7)
- [ ] Conta de revisão e instruções de teste
- [ ] **Plano B declarado:** o produto permanece útil sem localização em segundo plano, em modo
      degradado real e declarado
- [ ] Três cotações reais de provedor de SMS
- [ ] Planilha de custo por **cadastro**, por **alerta** e por **falso positivo**, em três
      cenários de taxa — a simulação é obrigatória no onboarding e envia mensagem real, então o
      SMS é custo de aquisição antes de ser custo de operação
- [ ] Plano B de affordance, escrito **antes de contratar**, se o link for filtrado

## 6. ADRs que a fase produz

| ADR | Quando | Depende de |
|---|---|---|
| **0011 provisório** | durante a fase | M10 + cotações |
| **0007** temporização | após a medição | parecer de monitoramento (antes) + M1, M2, M3, M5, M9 |
| **0008** recurso principal de localização em segundo plano | após o teste de listagem | verificação do formulário real no Play Console |
| **0012** limiares de bateria e falso positivo | após a fase | M5, M8, M13, **M14** |
| **0005-B** `margem_de_rede` | após a fase | p99 da M1, por fabricante |

## 7. Onde a fase termina

Documento 5, Fase 0 — vinte e um critérios de aceite, dos quais o último sustenta a isenção de
todos os outros:

> ☐ **nenhum arquivo promovido para a árvore de produção.**

As condições de interrupção, que podem encerrar o projeto em vez da fase, estão no §10 de
`metodo-de-medicao.md`.
