# Documento 1 — Visão do Produto e Modelo de Negócio

## 1. Identificação do documento

**Projeto:** Aplicativo de proteção pós-roubo para dispositivos móveis
**Nome provisório do produto:** Modo Rua
**Versão:** 2.1 | **Substitui:** versões 1.0 e 2.0
**Status:** Documento estratégico vigente
**Plataforma prioritária:** Android nativo
**Posição na hierarquia:** nível 6 (Documento 4, núcleo §0)
**Origem das alterações:** Resolução do ARB aos achados ARB-001 a ARB-064 (v2.0) e ARB2-001 a ARB2-033, MOD-01, MOD-02 e ADD-01 a ADD-07 (v2.1).

---

# 2. Resumo executivo

Aplicativo móvel de segurança voltado ao mercado brasileiro, com prioridade para Android e lançamento inicial no Rio de Janeiro.

O produto não pretende impedir fisicamente o roubo. Sua finalidade é reduzir as consequências do momento em que o usuário perde o controle físico do celular.

A promessa principal:

> **Proteger a vida digital, financeira e pessoal do usuário quando ele perde o controle físico do celular.**

O primeiro produto é um aplicativo Android nativo, com painel web como ferramenta complementar. A estratégia não é escalar nacionalmente com recursos próprios: é construir um MVP funcional, validar viabilidade técnica, conseguir os primeiros usuários, demonstrar intenção de pagamento e, depois, buscar investimento.

---

# 3. Visão do produto

## 3.1 O que estamos construindo

Uma plataforma de proteção móvel pós-roubo, cuja primeira entrega é um aplicativo Android capaz de:

- permitir a ativação manual de um Modo Rua;
- solicitar confirmações periódicas e detectar ausência de resposta;
- registrar eventos de forma imutável, mesmo sem rede;
- compartilhar a última localização com contatos autorizados durante emergência;
- executar um protocolo progressivo e reversível;
- permitir acionamento manual imediato de emergência;
- orientar o bloqueio de contas, chip, bancos e serviços;
- proteger informações internas do próprio aplicativo;
- funcionar parcialmente sem conexão e sincronizar depois.

### 3.1.1 Duas garantias distintas

O produto opera com duas garantias que **não** podem ser apresentadas como uma só:

| Garantia | O que cobre | Depende de rede |
|---|---|---|
| **Local** | Ativar, registrar, temporizar, persistir e sobreviver a reinício | Não |
| **Externa** | Alguém fora do aparelho descobrir que o usuário parou de responder | **Sim, sempre** |

A garantia externa depende do backend ter recebido a sessão. Uma ativação feita sem sinal fica em proteção reduzida até o aplicativo conseguir conexão, e **a interface diz isso com essas palavras**. Nenhum material de comunicação, tela ou peça de marketing pode sugerir o contrário.

## 3.2 O que não estamos construindo

O projeto não será apresentado como: sistema capaz de impedir roubo; garantia de recuperação do aparelho; substituto do Encontrar Meu Dispositivo, do Celular Seguro ou de seguro; solução capaz de controlar integralmente o Android; ferramenta de vigilância oculta ou monitoramento clandestino; aplicativo que bloqueia bancos ou PIX sem integração oficial; aplicativo que apaga remotamente qualquer aparelho em qualquer circunstância.

A comunicação comercial evita promessas absolutas e evita prazos exatos que a plataforma não garante.

---

# 4. Problema central

O celular concentra parte significativa da vida pessoal e financeira do usuário. Quando alguém perde o controle físico do aparelho, o risco vai muito além do valor do dispositivo: acesso a aplicativos bancários e PIX, e-mail principal, recuperação de senhas, WhatsApp, fotos e documentos, autenticadores, fraude, chantagem, engenharia social com contatos, perda de evidências, demora para bloquear contas e, sobretudo, a dificuldade de saber o que fazer nos primeiros minutos.

As soluções existentes funcionam de forma fragmentada: o Google localiza, o Celular Seguro auxilia em bloqueios, cada banco tem seu protocolo, seguradoras cobrem parte do prejuízo, e familiares dependem de mensagens manuais.

O problema a resolver é a ausência de uma experiência centralizada, preventiva e adaptada à realidade brasileira.

---

# 5. Público inicial

## 5.1 Público prioritário

Usuários Android **maiores de 18 anos** que circulam em grandes centros urbanos, usam bancos digitais, fazem PIX com frequência, dependem do celular para trabalhar, possuem aparelho de valor relevante e aceitariam pagar por uma camada adicional de segurança.

## 5.2 Segmentos de validação

- **A — Profissionais que trabalham na rua:** motoristas, entregadores, representantes, corretores, vendedores externos, técnicos, profissionais de saúde, autônomos.
- **B — Financeiramente expostos:** usuários frequentes de PIX, pequenos empresários, comerciantes, profissionais liberais, pessoas com limites bancários elevados.
- **C — Aparelhos de maior valor:** usuários premium, compradores recentes, quem já contratou seguro.
- **D — Adultos que cuidam de adultos:** casais, adultos responsáveis por idosos, grupos familiares **compostos apenas por maiores de 18 anos**. Proteção de menores está fora do MVP (§21.3) e exige avaliação jurídica e de segurança específica.
- **E — Já foram vítimas:** pessoas que sofreram roubo ou furto, tiveram prejuízo ou perderam acesso a contas.

## 5.3 Região inicial

Rio de Janeiro, pela elevada percepção de insegurança, identificação do público com o problema, possibilidade de comunicação e testes locais e acesso direto do fundador ao mercado. Expansão para outras capitais somente após validação técnica e comercial no primeiro mercado.

---

# 6. Proposta de valor

## 6.1 Principal

> **Proteger a vida digital, financeira e pessoal do usuário quando ele perde o controle físico do celular.**

## 6.2 Funcional

Reduzir o tempo entre o evento e as medidas de proteção; avisar pessoas autorizadas; registrar a última localização disponível; orientar ações emergenciais; centralizar o protocolo de resposta; diminuir a exposição de dados e contas.

## 6.3 Emocional

Preparação, redução de ansiedade, resposta mais rápida, proteção para quem se importa, controle em situação de crise e organização do que fazer.

## 6.4 Posicionamento

> **Uma central de proteção da sua vida digital em situações de roubo, furto, perda ou coação.**

Linhas alternativas: "Você pode perder o aparelho. Não precisa perder sua vida digital." / "Seu protocolo de segurança para quando o celular sai das suas mãos."

---

# 7. Modo Rua

## 7.1 Definição e ativação

O Modo Rua é o principal modo operacional. **No MVP, a ativação é manual.** Ativação programada, sugerida ou automática depende de sinais contextuais que estão fora do MVP e exigem permissões e declarações adicionais de loja (§21.3).

Ativar o Modo Rua não é um estado de emergência: é uma sessão de acompanhamento com confirmações periódicas.

## 7.2 Sinais de contexto — fora do MVP

Localização, saída de área segura, movimento, velocidade, Wi-Fi conhecido, desconexão de Bluetooth, padrão de uso, tempo sem interação, reinicialização, retorno de conexão e bateria são candidatos futuros. Cada um carrega permissão, declaração de loja e custo de bateria próprios, mapeados em matriz antes de qualquer inclusão (Documento 2, §34.5).

## 7.3 Suspeita

A avaliação de suspeita e as transições correspondentes pertencem ao protocolo do servidor. Os estados canônicos são os do **Documento 2, §10**. Este documento não define máquina de estados própria e não usa o termo "Modo Suspeito".

Nenhuma condição de suspeita executa ação destrutiva.

---

# 8. Check-in e ausência de resposta

## 8.1 Definição

Durante a sessão, o sistema solicita periodicamente:

> **Confirme que você ainda está com o celular.**

A confirmação exige **confirmação forte**: biometria ou o **código de bloqueio do próprio aparelho**, e a confirmação é assinada por chave que só funciona depois dessa autenticação. Um toque simples não conclui um check-in, e tocar na ação da notificação apenas abre a tela de confirmação. Motivo: o agressor com o aparelho desbloqueado pode tocar; não pode autenticar.

Limite honesto, declarado em vez de escondido: isso não protege contra **coação**. Quem obriga a vítima a desbloquear obtém confirmação válida. A senha de coação permanece fora do MVP (§9).

## 8.2 Intervalos e escalonamento

O intervalo de confirmação é configurável pelo usuário. O sistema Android pode atrasar o pedido; por isso existem um período de tolerância no aparelho e uma verificação independente no servidor.

> **Nenhum material, tela ou peça de comunicação declara minutos exatos enquanto a medição da Fase 0 não autorizar (ADR-0007).**

Internamente, porém, o tempo tem orçamento: intervalo, tolerância, margem de rede, janela de reconciliação, processamento e entrega somam, e a soma tem teto declarado e medido (Documento 2, §12.5). Não declarar minutos ao público não autoriza deixar de saber quantos são.

A ausência de confirmação não é decidida pelo aparelho: ele registra a ausência, e o servidor decide o que ela significa.

## 8.3 Risco de excesso de notificações

Pedir confirmação com frequência excessiva leva o usuário a ignorar, desativar, cancelar, confirmar sem atenção ou considerar o produto invasivo. O produto equilibra segurança, conveniência, bateria, privacidade e taxa de falso positivo, e mede as duas camadas de falso positivo separadamente (§25.1).

---

# 9. Senha de coação — fora do MVP

**Fora do MVP.** A senha de coação só será considerada após threat model específico, teste de usabilidade, revisão jurídica e avaliação de violência doméstica (Documento 3, §28.3). Não aparece em nenhuma jornada do produto atual e nenhum agente a prototipa por iniciativa própria.

---

# 10. Cercas de segurança

## 10.1 Escopo real

**Cercas de proximidade** (casa, trabalho, trajeto curto) usam a API de geofencing, com latência da ordem de minutos, dependência de localização em segundo plano e sensibilidade a otimizações de fabricante. São o único tipo previsto, e mesmo assim condicionadas à medição da Fase 0.

A cerca **não** é barrada pela política de localização em segundo plano: a política admite um conjunto de recursos centrais, desde que todos sejam promovidos na descrição. O que é único é o recurso principal informado no **formulário** de declaração (Documento 2, §34.5). A versão 2.0 confundia as duas coisas e, com isso, teria descartado a cerca por causa de uma regra que a loja não tem.

Referências a bairro, cidade, estado e país **não são geofences**: são inferências de região, com outra frequência, outro custo e outra precisão. Estão fora do MVP.

## 10.2 O que a cerca pode alterar

Frequência de verificação, nível de coleta de localização e sugestões ao usuário. Nunca ações destrutivas nem bloqueio de aplicativos de terceiros.

## 10.3 Limitações

O produto não promete bloquear PIX, bancos ou outros aplicativos sem integração oficial. O que pode fazer: orientar, exibir alertas, abrir atalhos, acionar protocolos próprios, exigir autenticação dentro do aplicativo e alterar como o próprio produto opera.

---

# 11. Cofre de proteção

Espaço interno para dados selecionados pelo usuário: contatos de emergência, informações críticas, códigos de recuperação, dados do aparelho, lista de contas a bloquear. Protegido pela chave `K_leitura` (Documento 2, §14.3), exigindo autenticação validada pelo hardware.

Ocultar ou bloquear aplicativos de terceiros permanece fora do escopo: um aplicativo comum não consegue fazê-lo sem permissões que o produto não terá. A evolução prevista é orientação, atalhos e checklist, não controle de terceiros.

---

# 12. Acionamento manual de emergência

É o caminho de emergência **mais confiável do produto**, porque não depende de detecção, temporização nem inferência. Faz parte do núcleo do MVP e é entregue na **Fase 3, antes da automação**.

O acionamento abre o protocolo diretamente no estado `ALERTANDO`: registra o evento, notifica os contatos autorizados pela cascata de canais, compartilha a última localização conhecida com idade e precisão, e disponibiliza a orientação pós-roubo.

Formas previstas: botão no aplicativo e ação na notificação persistente da sessão — aqui um toque único basta, porque escala na direção segura e reversível. Sequências de teclas do sistema, gravação, câmera e captura silenciosa não são tratadas como disponíveis.

Requisito técnico com efeito de produto: o acionamento é **idempotente por chave gerada antes do primeiro envio** (Documento 2, §16.3). Sem isso, alguém em pânico com rede ruim abre dois protocolos e dispara dois avisos ao contato.

---

# 13. Ações destrutivas — fora do MVP

Bloquear aplicativos de terceiros, apagar tokens bancários, encerrar contas alheias, apagar dados e restauração de fábrica **não** compõem o produto atual, por risco de falso positivo, irreversibilidade, limitações do Android, responsabilidade jurídica, restrições de loja e perda da possibilidade de localizar o aparelho.

O protocolo começa e permanece em ações reversíveis: alertar, registrar, localizar, compartilhar temporariamente, orientar, proteger dados internos e permitir encerramento autenticado.

---

# 14. Protocolo de emergência

A estrutura de estados, transições, precedência e reversibilidade é definida no **Documento 2, §10.2**, e é canônica. Este documento não mantém níveis próprios.

Regras de produto que valem sobre qualquer implementação:

- nenhuma ação irreversível automática;
- encerramento sempre possível pelo titular autenticado, **sem depender do aparelho perdido**;
- registro de quem realizou cada ação;
- consideração explícita de ausência de internet, bateria esgotada e notificação ignorada;
- linguagem que não expõe a vítima nem provoca retaliação;
- trilha de auditoria consultável pelo titular.

---

# 15. Jornada normal

## 15.1 Cadastro

1. Instala o aplicativo.
2. Cria conta e verifica e-mail.
3. Configura a confirmação forte: biometria, ou o código de bloqueio do aparelho como caminho sempre disponível. **O papel de um PIN interno próprio do aplicativo, e a exigência de que o aparelho tenha bloqueio de tela configurado, estão pendentes de decisão** (Documento 2, §14.3) — há custo de mercado em exigir bloqueio de tela no Brasil, e há custo de segurança em não exigir.
4. Ativa biometria (opcional, conveniência).
5. Concede permissões em contexto, **incluindo notificações**.
6. Convida um contato de confiança e **aguarda o aceite**.
7. **Executa a simulação do protocolo.**
8. Escolhe o plano.
9. Ativa o Modo Rua.

## 15.2 Uso diário

Sai de casa, ativa o Modo Rua, vê o estado de cobertura, recebe poucos pedidos de confirmação, confirma com biometria ou PIN, retorna, encerra a sessão com autenticação e vê um resumo.

## 15.3 O que a jornada normal deve transmitir

Simplicidade, baixo consumo, pouca interferência, clareza sobre o que está e o que não está protegido, e transparência sobre a cobertura.

---

# 16. Jornada de suspeita

1. O prazo de confirmação vence sem resposta.
2. O aparelho registra a ausência e sincroniza, se puder.
3. O servidor, independentemente do aparelho, avalia o prazo vencido.
4. O protocolo abre em suspeita, **sem notificar ninguém**.
5. Uma janela de reconciliação aguarda eventos atrasados.
6. Se o usuário confirmar nesse intervalo, o protocolo encerra como falso positivo e é classificado.
7. Se não houver confirmação, o protocolo passa a alertar e os contatos são acionados.

---

# 17. Jornada de roubo confirmado

1. O aparelho é roubado ou furtado.
2. O usuário não responde ao check-in, ou aciona manualmente a emergência.
3. O servidor abre e escala o protocolo, mesmo que o aparelho esteja desligado, sem sinal ou sem bateria.
4. O contato de confiança recebe o alerta pela cascata de canais e registra ciência.
5. O painel apresenta última localização com idade e precisão, horário da última comunicação, estado do protocolo e orientação.
6. O titular confirma o roubo ou encerra com segurança, sempre por autenticação e sem depender do aparelho perdido.
7. O sistema orienta bloqueio do chip, contato com bancos, uso do Celular Seguro e do Encontrar Meu Dispositivo, troca de senhas, encerramento de sessões e registro de ocorrência.
8. O histórico é preservado.

---

# 18. Contatos de confiança

## 18.1 Função

Pessoas autorizadas a receber alertas e acessar informações limitadas durante um protocolo autorizado.

## 18.2 Requisitos

Convite com expiração; aceite explícito com criação de conta e segundo fator no momento do aceite; exibição clara do que será compartilhado; revogação imediata; registro de todos os acessos, visível ao titular; limitação de período; nenhuma inclusão silenciosa.

## 18.3 O que o contato pode e não pode

**Pode:** receber alerta; registrar ciência; consultar a última localização durante protocolo autorizado; acompanhar o estado do protocolo; receber orientação.

**Não pode:** alterar o estado do protocolo, encerrar alerta, alterar a conta, acessar histórico completo ou manter acesso após o encerramento.

## 18.4 Prevenção de abuso

O recurso não pode ser usado para vigiar parceiros, monitorar pessoas sem consentimento, perseguir usuários, acompanhar localização permanentemente ou operar de forma oculta. Convites têm limite por janela, recusa é respeitada e reenvio a quem recusou é bloqueado.

---

# 19. Painel web

Extensão do aplicativo, acessada pelo titular, por contato autorizado e, em casos limitados e auditados, pelo suporte.

**Escopo:** estado do aparelho e da cobertura; última comunicação; localização durante protocolo, com idade e precisão; eventos; contatos e acessos recentes; dispositivos; protocolo; encerramento autenticado; orientações; assinatura; **exclusão da conta**.

Todo acesso é autenticado com segundo fator, registrado, limitado no escopo e temporário. Consulta de localização exige elevação de autenticação.

---

# 20. Modelo de negócio

## 20.1 Modelo principal

Assinatura, sem plano gratuito permanente no lançamento, com período de teste.

## 20.2 Oferta inicial

- **Mensal:** R$ 14,90 a R$ 19,90.
- **Anual:** cerca de R$ 149,90.
- **Familiar (posterior, somente adultos):** R$ 199,90 a R$ 299,90 por ano.

Preços a validar. O custo por **cadastro**, por **alerta** e por **falso positivo** — incluindo SMS — é calculado na **Fase 0**, não na Fase 5: a simulação obrigatória do onboarding (§15.1) envia mensagem real, então o SMS é custo de aquisição antes de ser custo de operação, e é essa conta que decide se a faixa de R$ 14,90 a R$ 19,90 se sustenta.

## 20.3 Teste gratuito

Sugestão inicial: **14 dias**. Durante o teste, o usuário deve ativar o Modo Rua, cadastrar contato, **executar a simulação do protocolo** e usar o painel.

## 20.4 Cobrança e proteção em andamento

**Sessão ativa e protocolo aberto seguem até o fim, qualquer que seja o estado da assinatura.** A restrição incide apenas na ativação de nova sessão.

Permanecem sempre disponíveis, sem assinatura: encerrar protocolo, consultar o próprio histórico, exportar dados e excluir a conta.

## 20.5 Por que não há plano gratuito permanente

Infraestrutura, custo de mensageria, suporte, risco de abuso, armazenamento, posicionamento e foco em validar disposição de pagamento.

---

# 21. Funcionalidades do MVP

## 21.1 Núcleo obrigatório

Cadastro; login; recuperação de conta; **confirmação forte por biometria ou código de bloqueio do aparelho** (papel do PIN interno pendente de decisão, §15.1); Modo Rua manual; temporizador persistido; check-in com confirmação forte; registro de ausência; **indicador de estado de cobertura**; eventos locais; armazenamento offline; sincronização com detecção de lacunas; localização com idade e precisão; notificações; contato de confiança com convite e aceite — **quantos contatos no MVP está pendente de decisão** (Documento 2, §10.2); **cascata de alerta incluindo SMS**; **acionamento manual de emergência**; **simulação de protocolo**; vigilante de prazo no servidor; painel web com segundo fator; histórico básico; assinatura; onboarding; **exclusão de conta no aplicativo e por página web**; **exportação de dados e atendimento dos demais direitos do titular** (Fase 2 — antes desta versão eram exigidos sem fase que os construísse); política de privacidade; termos; logs de segurança; analytics mínimo.

## 21.2 Desejáveis no MVP

Cerca de proximidade de casa; página de emergência; integração por links com serviços oficiais.

**A orientação pós-roubo (`recovery_guide`) saiu daqui e virou núcleo**, entregue na Fase 4: ela é o efeito de uma transição canônica do protocolo (Documento 2, §10.2, linha 10, "libera orientação"), e uma transição obrigatória não pode ter como efeito um recurso opcional.

## 21.3 Fora do MVP

Bloquear PIX ou bancos; esconder aplicativos; alterar o PIN do Android; impedir desinstalação; apagar tokens de terceiros; sair silenciosamente de contas; gravação oculta; câmera oculta; selfie silenciosa; restauração de fábrica automática; senha de coação; **comandos remotos genéricos**; **sinais contextuais (Wi-Fi, Bluetooth, movimento)**; **ativação automática**; **titulares menores de 18 anos**; **Play Integrity como bloqueio**; inteligência artificial avançada; integração direta com bancos; seguro; versão iOS; proteção para notebooks; monitoramento empresarial; automações destrutivas.

---

# 22. Funcionalidades posteriores

**Evolução técnica:** ativação assistida por contexto, check-in adaptativo, regras personalizadas, múltiplas cercas, múltiplos contatos, plano familiar entre adultos, histórico ampliado, integrações oficiais, widget e atalhos.

**Expansão:** iOS limitado, tablet, conta familiar, conta empresarial, parcerias com seguradoras e operadoras, integração com bancos, suporte emergencial, recuperação assistida, API B2B, white label.

---

# 23. Diferenciação

O produto não é um rastreador: é uma central de contingência. Elementos: Modo Rua; protocolo progressivo e reversível; **verificação independente no servidor**; foco brasileiro; rede de contatos consentida e auditável; painel; orientação pós-roubo; experiência em português; foco na vida financeira; combinação de prevenção e resposta; suporte.

**Risco de comoditização:** Apple ou Google podem incorporar funções semelhantes. Ativos complementares a construir: marca, confiança, base de clientes, protocolos, distribuição, suporte, parcerias e experiência localizada.

**Concorrentes indiretos:** Encontrar Meu Dispositivo, recursos antirroubo do Android, Celular Seguro, Cerberus, Prey, apps de localização, seguros, bancos e operadoras.

---

# 24. Hipóteses

## 24.1 Principais

1. Existe público disposto a pagar por proteção adicional.
2. O medo de perdas financeiras supera o de perder o aparelho.
3. O usuário entende o valor de um protocolo centralizado.
4. O público aceita conceder permissões, inclusive notificações e localização em segundo plano.
5. O produto opera com consumo de bateria aceitável.
6. A taxa de falso positivo externo é tolerável.
7. O usuário continua assinando após o período inicial.
8. O Rio de Janeiro é um bom mercado de teste.
9. **O Android permite uma temporização suficientemente confiável.**
10. **A Google Play aprova o recurso principal declarado de localização em segundo plano** (e a descrição pode promover o conjunto de recursos centrais, §10.1).
11. **Existe canal capaz de alertar o contato com urgência a custo viável.**
12. O produto cresce por indicação.

## 24.2 Hipóteses críticas

Viabilidade da temporização; aprovação da loja; canal de alerta; disposição de pagamento; retenção; consumo de bateria; confiabilidade; diferenciação diante de soluções gratuitas.

As **três primeiras** são testadas na Fase 0, e não em fases posteriores. A hipótese do canal de alerta ficava para a Fase 3 na versão 2.0 — isto é, depois de construir duas fases e meia — embora seja condição de interrupção do projeto (§32). É trabalho de mesa: cotações, teste de entrega por operadora e cálculo de custo por cadastro, por alerta e por falso positivo. O risco específico do Brasil não é o preço, é a **filtragem de SMS com link** pelas operadoras, e todo o desenho de alerta depende de um link curto no corpo da mensagem.

---

# 25. Métricas de sucesso

## 25.1 Produto

Conclusão de onboarding; permissões concedidas; primeira sessão; sessões por usuário; check-ins concluídos; **percentual de pedidos de confirmação não vistos**; **falso positivo interno** (escalonamento indevido sem acionar contato) e **falso positivo externo** (contato acionado indevidamente), medidos separadamente; **tempo até a primeira ciência do contato**; atraso de alerta; eventos sincronizados; lacunas detectadas; falhas em segundo plano; consumo; taxa de sucesso por fabricante e por versão do Android.

## 25.2 Comerciais

Visitantes, cadastro, custo por lead, instalação, ativação, teste iniciado, conversão, CAC, receita recorrente, churn, retenção, LTV, payback, mensal versus anual, indicação, cancelamento e reembolso.

## 25.3 Regra de apresentação

> Toda métrica declarada acompanha o número de observações. **Percentual sem denominador é proibido** em qualquer material interno ou externo. Sinal qualitativo e tração estatística são apresentados separadamente, e CAC, churn e retenção não são apresentados como estáveis com amostra pequena.

---

# 26. Limitações conhecidas

## 26.1 Técnicas

O Android restringe execução em segundo plano e fabricantes restringem mais; **a proteção externa depende do backend e de uma conexão que já tenha ocorrido**; **o aparelho encerrado à força pelo usuário ou pelo gerenciador do fabricante deixa de executar até ser reaberto**; **a entrega de notificação não é garantida e pode ser silenciada pelo Não Perturbe**; **a revogação da permissão de alarme exato, se esse mecanismo for escolhido, cancela os disparos já agendados**; **a proteção do histórico e a confirmação forte dependem de o aparelho ter bloqueio de tela configurado**; **entre a captura de uma localização e a confirmação de que o servidor a recebeu, esse dado fica legível a quem controlar o aparelho desbloqueado — é o preço de conseguir enviá-lo sem o usuário presente, e a janela é curta por desenho**; localização pode falhar; internet pode faltar; o celular pode ser desligado; o chip pode ser removido; permissões podem ser revogadas; o aplicativo pode ser desinstalado; a bateria pode acabar.

## 26.2 Comerciais

Soluções gratuitas; resistência a pagar; preocupação com privacidade; baixa confiança em empresa nova; custo de aquisição; churn; necessidade de educar o mercado; risco de comunicação gerar medo excessivo; dificuldade de provar eficácia sem casos reais.

## 26.3 Jurídicas

LGPD; tratamento de localização; bases legais por finalidade; retenção; responsabilidade por falha; uso por terceiros; perseguição; compartilhamento; acessos do contato; exclusão; suporte a incidentes; comunicação publicitária.

## 26.4 Operacionais

Suporte fora do horário comercial; volume de alertas; disponibilidade; incidentes; fraude; abuso; **continuidade com equipe de uma pessoa**; manutenção contínua; atualizações do Android; mudanças da Play Store.

---

# 27. Princípios de produto

1. Não prometer impedir roubo.
2. Não prometer recuperação.
3. Não prometer proteção absoluta.
4. Priorizar ações reversíveis.
5. Evitar destruição automática.
6. Exigir consentimento onde ele é a base adequada, e não usá-lo como base genérica da função essencial.
7. Minimizar dados.
8. Proteger contra abuso.
9. Manter transparência.
10. Registrar eventos críticos.
11. Testar em aparelhos reais.
12. Tratar segurança como requisito central.
13. Não depender de acessibilidade como atalho.
14. Não esconder funcionalidades da loja.
15. Não criar falsa sensação de segurança.
16. **Não prometer o que depende de canal sem garantia.**
17. **Declarar o estado de cobertura ao usuário, sempre.**

---

# 28. Fases do projeto

As fases, critérios de aceite, gates e condições de bloqueio são definidos **exclusivamente pelo Documento 5**. Este documento não mantém etapas próprias.

---

# 29. Estratégia de crescimento

**Inicial:** Rio de Janeiro; tráfego segmentado; conteúdo educativo; depoimentos autorizados; parceria com criadores; programa de indicação; comunidades locais; profissionais que trabalham na rua.

**Expansão:** novas capitais; plano familiar entre adultos; seguradoras; operadoras; bancos; empresas; white label; B2B2C.

---

# 30. Riscos principais

| Risco | Mitigação |
|---|---|
| Temporização insuficiente no Android | Spike medido na Fase 0; vigilante no servidor; faixas em vez de minutos exatos |
| Rejeição na loja | Função única declarada, disclosure, vídeo, plano B em modo degradado |
| Canal de alerta inadequado | Viabilidade medida na Fase 0, incluindo filtragem de SMS com link por operadora e custo por cadastro, alerta e falso positivo; plano B de affordance; medição de ciência |
| Alerta que ninguém vê | Estado `SEM_CIENCIA`, teto de tentativas e aviso ao titular; a métrica registra o caso sem ciência em vez de ficar indefinida |
| Falsos positivos | Protocolo progressivo, confirmação forte, janela de reconciliação, guarda de anomalia |
| Abuso por terceiros | Convite, aceite, concessões limitadas, auditoria visível, revogação |
| Exposição de dados | Criptografia por campo, retenção curta local e remota, autorização por recurso, auditoria |
| Baixa retenção | Pouca fricção, simulação que demonstra valor, benefício contínuo |
| Concorrência gratuita | Centralização, protocolo, suporte, foco brasileiro, marca |
| Continuidade com uma pessoa | Plantão mínimo, runbooks, contato técnico de contingência, plano de encerramento |

---

# 31. Critérios para considerar a ideia validada

- o núcleo funciona em aparelho físico, nas marcas da matriz mínima;
- o alerta dispara com o aparelho desligado, sem sinal ou com bateria esgotada;
- o fluxo offline funciona e nenhum evento confirmado localmente é perdido;
- o usuário compreende a proposta e o estado de cobertura;
- há intenção de pagamento demonstrada;
- o consumo de bateria fica dentro do limiar vigente;
- o falso positivo externo fica dentro do limiar vigente;
- as funções passaram por análise jurídica;
- o produto tem caminho de aprovação na Play Store.

> Nenhum critério é considerado atendido com amostra que não sustente a afirmação. Retenção, churn, conversão e CAC exigem base suficiente e declaração de n.

---

# 32. Critérios para interrupção ou reformulação

A função principal não é permitida; nenhum mecanismo de temporização entrega atraso compatível com a promessa; não existe canal de alerta viável a custo aceitável; o produto não funciona com tela bloqueada; o consumo é elevado; usuários recusam permissões; ninguém aceita pagar; o churn é excessivo; os falsos positivos externos são frequentes; o risco jurídico é alto; a Play Store impede a operação.

---

# 33. Conclusão

A oportunidade não está em criar mais um rastreador, e sim uma central de contingência pós-roubo adaptada ao Brasil.

A prioridade permanece:

> **Alertar, registrar, localizar, orientar e coordenar uma resposta segura.**

Com uma correção que atravessa todo o produto: **alertar não é uma capacidade do aparelho**. É uma capacidade do sistema, e depende do servidor ter recebido a sessão antes de o aparelho desaparecer. Tudo o que o produto promete a terceiros nasce dessa dependência, e é por isso que ela é declarada ao usuário em vez de escondida.

Ações destrutivas, secretas ou dependentes de controle profundo do sistema não compõem o MVP. O objetivo não é garantir que o aparelho não será roubado: é garantir que, quando o usuário perder o controle físico do celular, ele e seus contatos saibam rapidamente que algo aconteceu, tenham as informações necessárias e consigam reduzir as consequências.
