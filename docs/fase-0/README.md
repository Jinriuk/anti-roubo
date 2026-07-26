# Fase 0 — Viabilidade técnica

**Estado: NÃO INICIADA.** Pré-condições de entrada em aberto — ver §2.
**Autoridade da fase:** Documento 5, Fase 0. Este arquivo não define escopo, critério de aceite
nem evidência; ele registra **situação** e **pendências**.

---

## 1. O que a fase é

Medir as hipóteses de plataforma e de loja que podem inviabilizar o produto, e produzir as
declarações da Google Play.

> **O entregável real é matriz de capacidade, medições e ADRs. Não é software.**

Todo código da fase é descartável por definição, vive em `spike/` e não é promovido (ADR-0003).
A isenção vale para as regras de **código**, não para as de **evidência** — nesta fase a
evidência **é** o entregável: o p99 medido vira `margem_de_rede` em produção e os limiares de
bateria viram gate de release por ADR-0012.

**Três hipóteses críticas** são testadas aqui e em nenhuma fase posterior (Documento 1, §24.2):
viabilidade da temporização · aprovação da loja · **canal de alerta**.

## 2. Pré-condições de entrada — situação

| # | Pré-condição | Fonte | Situação |
|---|---|---|---|
| 1 | **ADR-0001** aprovado | Doc 5, Fase 0 (Dependências) | `docs/adr/0001-stack-base.md` — **PROPOSTO**, aguarda aceite |
| 2 | **ADR-0002** aprovado | idem | `docs/adr/0002-minsdk-targetsdk.md` — **PROPOSTO**, com um critério de dois satisfeito |
| 3 | **ADR-0003** aprovado | idem | `docs/adr/0003-estrutura-descartavel-fase-0.md` — **PROPOSTO**, aguarda aceite |
| 4 | **Timebox declarado** (alvo e limite duro, em semanas) | Doc 5, §2 — *"fase sem timebox não inicia"* | ❌ **não existe** |
| 5 | Três ou mais aparelhos reais, dois abaixo de API 33, um no Android atual | Doc 5, Fase 0 | ❌ não confirmados — e a matriz tem lacuna, ver M9 |
| 6 | Servidor de teste | idem | ❌ não confirmado |
| 7 | Conta no Play Console | idem | ❌ não confirmada |
| 8 | Contas de teste em três operadoras | idem | ❌ não confirmadas |
| 9 | **Método de medição escrito e revisado antes da coleta** | Doc 5, Fase 0 | `metodo-de-medicao.md` — **escrito**, aguarda revisão |
| 10 | **Template de evidência do núcleo §9** | Doc 5, §6; mód. 40 §5 | ❌ **ausente do repositório** — ver `docs/agentes/00-nucleo.AUSENTE.md` |

## 3. Decisões que só o fundador pode tomar

Reunidas aqui para não ficarem espalhadas. Cada uma bloqueia ou altera trabalho concreto.

| ID | Decisão | Bloqueia |
|---|---|---|
| **D1** | Enviar `00-nucleo.md` v2.3, ou operar com a ausência declarada | Template de evidência (§9); implantação dos gates de CI (§11); conferência dos marcadores (§0) |
| **D2** | Incorporar ao Documento 5 as três medições do ARB4-007 (M12, M13, M14) — condição declarada pela ARB4 para iniciar a fase | Fechamento do método de medição. Depois da coleta, custa recoleta |
| **D3** | Aplicar ou não os seis itens do portão da ARB4 antes de iniciar | Se não aplicar, a Fase 1 herda o ARB4-001 (Crítico) no §16.8 |
| **D4** | Aceitar os ADRs 0001, 0002 e 0003 | Entrada formal da fase |
| **D5** | Incluir a distribuição de versões do Android no público-alvo (M15) na mesma pesquisa da M11 | Fecha o segundo critério do ADR-0002 antes da revisão |
| **D6** | Declarar a faixa provisória de intervalo de check-in (mínimo e máximo) — hoje é propriedade do ADR-0005-A, cujo prazo é *depois* desta fase | Fixação do intervalo a medir em M1; e o timebox, que é dominado por ele |
| **D7** | Resolver a lacuna da matriz: sem aparelho em Android 16, a M9 não é executável | Compra de hardware. Depois é dinheiro gasto na configuração errada |
| **D8** | Confirmar o recorte de papéis: agente escreve método, instrumento, entregáveis de loja e ADRs; **humano identificado executa a coleta** | Todo o cronograma da fase |

## 4. Entregáveis de mesa — não dependem de hardware

Podem ser produzidos em paralelo à aquisição de aparelhos:

- [ ] **Parecer escrito de classificação como aplicativo de monitoramento** — e ele vem **antes**
      do ADR-0007, porque pode eliminar candidato de temporização por política
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

## 5. ADRs que a fase produz

| ADR | Quando | Depende de |
|---|---|---|
| **0011 provisório** | durante a fase | M10 (entrega de SMS por operadora) + cotações |
| **0007** temporização | após a medição | parecer de monitoramento (antes) + M1, M2, M3, M5, M9 |
| **0008** recurso principal de localização em segundo plano | após o teste de listagem | verificação do formulário real no Play Console |
| **0012** limiares de bateria e falso positivo | após a fase | M5, M8, M13, M14 |
| **0005-B** `margem_de_rede` | após a fase | p99 da M1, por fabricante |

## 6. Onde a fase termina

Documento 5, Fase 0 — dezessete critérios de aceite, dos quais o último é o que sustenta a
isenção de todos os outros:

> ☐ **nenhum arquivo promovido para a árvore de produção.**

E as condições de interrupção, que podem encerrar o projeto em vez da fase, estão no §9 de
`metodo-de-medicao.md`.
