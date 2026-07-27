# Fase 0 — Viabilidade técnica

**Estado: PRONTA PARA INICIAR, faltando duas condições** — ver §2.
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
| 5 | **Método de medição escrito e revisado antes da coleta** | ⏳ escrito (`metodo-de-medicao.md`); **falta a revisão e o aceite do fundador** |
| 6 | Template de evidência (núcleo §9) | ✅ disponível em `docs/agentes/00-nucleo.md` |
| 7 | **Timebox declarado** (alvo e limite duro, em semanas) — Doc 5, §2: *"fase sem timebox não inicia"* | ❌ **não existe** |
| 8 | Servidor de teste | ⏳ a confirmar |
| 9 | Conta no Play Console | ⏳ a confirmar |
| 10 | Contas de teste em três operadoras | ⏳ a confirmar |

**Duas condições travam a partida formal:** a revisão do método (#5) e o timebox (#7). As
condições #8 a #10 travam medições específicas (M10 e a sincronização do protótipo), não a fase
inteira — os entregáveis de mesa e os ensaios de plataforma não dependem delas.

## 3. Decisões tomadas em 2026-07-27

| ID | Decisão | Resultado |
|---|---|---|
| D1 | Núcleo | `00-nucleo.md` v2.3 no repositório. **A hierarquia do §0 põe o Documento 4 no nível 3, acima dos Documentos 5, 2 e 1** — `CLAUDE.md` corrigido |
| D2 | ADRs 0001, 0002 e 0003 | Aceitos, imutáveis a partir do aceite |
| D3 | Medições do ARB4-007 | Incorporadas ao Documento 5: M12, M13, M14, com critérios de aceite próprios |
| D4 | Faixa de intervalo de confirmação | **Valor provisório declarado: 15 min / 30 min / 60 min**, dono ADR-0005-A (núcleo §0) |
| D5 | Matriz de aparelhos | Lacuna do Android 16 resolvida; Documento 5 corrigido para quatro aparelhos |
| D6 | Seis itens do portão da ARB4 | Aplicados — ver `docs/auditoria/CHANGELOG-ARB4.md` |
| D7 | Distribuição de versões do Android | Incorporada como M15, fechando o segundo critério do ADR-0002 |
| D8 | Papéis | Confirmado pelo próprio template do núcleo §9: o agente prepara tudo e **não preenche resultado observado**; a coleta é de executor humano identificado |
| D9 | Issues | Backlog de consistência convertido em issues do repositório |

## 4. O timebox, e o número que o domina

O Documento 5, §2 exige alvo e limite duro em semanas, definidos **antes** do início.
O termo dominante é a **M1**, e ela é dominada pelo **máximo** da faixa de intervalo:

| Bloco | Calendário estimado |
|---|---|
| **M1** — atraso de disparo, três pontos de intervalo, dois estados de bateria | **~23 dias** de coleta contínua |
| M5, M8, M13 — consumo e heartbeat, em paralelo nos mesmos aparelhos | absorvido pela M1 |
| M2, M3, M6, M7 — ensaios curtos e discretos | ~3 a 5 dias |
| M9 — cota de job, Android 16 e 17 | ~2 dias |
| M4, M14 — campo, exigem deslocamento | ~3 a 5 dias |
| M10, M11, M15 — mesa e pesquisa, em paralelo | ~5 a 10 dias, sem hardware |
| Entregáveis de loja e parecer de monitoramento | ~5 dias, sem hardware |

**Alavanca declarada:** reduzir o máximo provisório de 60 para 30 min cortaria a M1 de ~23 para
~14 dias. É decisão de produto, não de engenharia, e pertence ao ADR-0005-A.

## 5. Entregáveis de mesa — não dependem de hardware

- [ ] **Parecer escrito de classificação como aplicativo de monitoramento** — vem **antes** do
      ADR-0007, porque pode eliminar candidato de temporização por política
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

As condições de interrupção, que podem encerrar o projeto em vez da fase, estão no §9 de
`metodo-de-medicao.md`.
