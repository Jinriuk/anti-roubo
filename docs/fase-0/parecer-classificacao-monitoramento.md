# Parecer — classificação do Modo Rua como aplicativo de monitoramento

**Entregável da Fase 0** (Documento 2, §34.6) · **Estado:** PROVISÓRIO — conclusão reforçada por
fonte oficial conferida pelo fundador; verificações operacionais pendentes
**Data:** 2026-07-27 · **Revisado:** 2026-07-27, com correções do fundador · **Autor:** agente
**Precede obrigatoriamente:** ADR-0007 (arquitetura de temporização)

> **Por que este parecer vem antes do ADR-0007.** Documento 2, §12.2 e §34.6: a política de
> aplicativos de monitoramento exige notificação persistente durante toda a execução, mais ícone
> único e divulgação na descrição — o que pode **eliminar candidato de temporização por política,
> não por medição**. Decidir o mecanismo antes de saber se a política se aplica seria medir
> candidatos que já estariam mortos.

---

## 0. Limitação de fonte — declarada antes de qualquer conclusão

**Não consegui ler as páginas oficiais.** A política de rede deste ambiente bloqueia
`support.google.com` e `developers.google.com` (403 no túnel do proxy, não bloqueio do Google).

O que usei: buscas na web que retornaram **trechos atribuídos às páginas oficiais**, com as URLs
citadas, corroborados por **fontes secundárias independentes** que relatam a mesma redação desde
o anúncio de setembro de 2020. Isso é mais que nada e **menos que ler a fonte**.

Consequência, pela regra do núcleo §4.6 e do módulo 30, §9 — *"afirmação de plataforma ou de
política de loja mais restritiva que a fonte também é invenção"*:

> **Toda afirmação normativa deste parecer carrega `VERIFICAR:` com a pergunta exata.** A
> conclusão é **provisória** e só se torna definitiva depois de conferida contra a página oficial,
> a partir da conta do Play Console — que é o item #10 da preparação da Fase 0.

> ✅ **Reforço em fonte oficial, conferido pelo fundador em 2026-07-27.** A política vigente do
> Google Play **limita** os aplicativos de monitoramento aceitáveis aos casos de **monitoramento
> parental de crianças** e **gerenciamento empresarial de funcionários**, e declara que esses
> aplicativos **não podem ser usados para rastrear outros adultos, mesmo com conhecimento ou
> consentimento e mesmo com notificação persistente**. As duas afirmações que sustentam este
> parecer — §1 (a) e (b) — deixam de ser secundárias.
>
> Permanecem pendentes as verificações **operacionais** do §6 (formulário real, *warning string*,
> categoria de segurança pessoal), que exigem a conta do Play Console.

Fontes consultadas, todas remetendo às páginas oficiais que não pude abrir:

- Play Console Help — *Malware* (política de stalkerware e aplicativos de monitoramento):
  `support.google.com/googleplay/android-developer/answer/9888380`
- Play Console Help — *Use of the isMonitoringTool Flag*:
  `support.google.com/googleplay/android-developer/answer/12955211`
- Play Console Help — *Understanding Google Play's Spyware policy*:
  `support.google.com/googleplay/android-developer/answer/14745000` — **não examinada; ver §6**
- Google for Developers — *Play Protect / PHA categories*:
  `developers.google.com/android/play-protect/phacategories`
- Play Console Help — *Developer Program Policy: September 16, 2020 announcement*:
  `support.google.com/googleplay/android-developer/answer/10065487`

---

## 1. O que a política diz, segundo as fontes consultadas

**a) A categoria "aplicativo de monitoramento aceitável" é fechada e estreita.**

> *"Apps exclusively designed and marketed for monitoring another individual — for example
> parents to monitor their children, or enterprise management for the monitoring of individual
> employees, provided they fully comply with the requirements — are the only acceptable
> monitoring apps."*

`VERIFICAR:` a redação vigente mantém "**the only acceptable monitoring apps**", e a lista
continua limitada a **pais→filhos** e **empresa→funcionário**? Existe alguma terceira categória
aceitável acrescentada desde 2020?

**b) Monitorar outro adulto não é aceitável nem com consentimento.**

> *"However, these apps cannot be used to track anyone else (a spouse, for example) even with
> their knowledge and permission, **regardless if persistent notification is displayed**."*

`VERIFICAR:` a cláusula "regardless if persistent notification is displayed" está no texto
vigente? É ela que decide todo este parecer.

**c) Requisitos impostos a quem É aplicativo de monitoramento.**

Flag `isMonitoringTool` no manifesto de todos os *version codes*, em todas as faixas · notificação
persistente **todo o tempo em que o aplicativo está em execução** · ícone único que identifica
claramente o aplicativo · divulgação da funcionalidade de monitoramento na descrição da loja ·
proibição de se apresentar como solução de espionagem ou vigilância secreta · proibição de
ocultar ou disfarçar o comportamento de rastreamento.

`VERIFICAR:` a lista está completa e a redação de cada item confere?

**d) Valores da flag e efeito de declará-la.** Os valores centrais previstos são
**`child_monitoring`** e **`enterprise_management`**. Existe também **`other`** — e ele **não é
uma opção neutra**: seu uso encaminha o aplicativo para **avaliação de eventual categoria de
isenção**, isto é, submete o produto a um julgamento de enquadramento em vez de evitá-lo.

Declarar a flag faz o Google Play informar ao usuário, por *warning string* do Play Protect, que
**há um aplicativo de monitoramento no aparelho**.

`VERIFICAR:` a *warning string* do Play Protect é acionada pela flag, e com que texto ao usuário?

---

## 2. O que o Modo Rua é, segundo os documentos do projeto

| Dimensão | Modo Rua | Fonte |
|---|---|---|
| Quem instala | **o próprio titular**, para a própria proteção | Doc 1, §3.1 |
| Quem é o sujeito protegido | **o titular** | Doc 1, §6.1 |
| O que o terceiro recebe | alerta, e acesso **temporário e escopado** à última localização, **apenas durante protocolo autorizado** | Doc 1, §18.3; Doc 2, §24 |
| Consentimento do terceiro | convite com **aceite explícito**, conta e MFA criados no aceite | Doc 2, §24 |
| Acesso contínuo | **não existe**; sem monitoramento permanente | Doc 2, §24 |
| Poder do contato | registra ciência. **Não altera estado, não encerra alerta, não vê histórico completo, não mantém acesso após o encerramento** | Doc 1, §18.3; Doc 3, §22.2 |
| Visibilidade | ícone visível, instalação visível, sem modo oculto, sem coleta silenciosa | Doc 3, §23.2; Doc 1, §21.3 |
| Auditoria | **todo acesso à localização é registrado e visível ao titular** | Doc 3, §34.4 |
| Revogação | imediata e acessível ao titular | Doc 3, §23.2 |
| Marketing | proibido posicionar-se como vigilância; **prevenção de stalking é requisito de produto** | Doc 3, §2, §24.1 |
| Faixa etária | **somente maiores de 18 anos**; menores fora do MVP | Doc 1, §5.1; Doc 3, §46 |

A frase que resume: **o Modo Rua não é uma pessoa observando outra. É uma pessoa pedindo que,
se ela parar de responder, alguém seja avisado.**

---

## 3. Análise

### 3.1 O produto não satisfaz a definição

A categoria da política se define por **finalidade e marketing**: *"exclusively designed and
marketed for monitoring another individual"*. O Modo Rua é desenhado e comercializado para
**proteger quem o instala**. O terceiro não é observado — o terceiro é **avisado**, e só quando o
titular deixou de responder ou acionou a emergência.

Nenhum dos três traços que caracterizam a categoria está presente: não há sujeito observado
distinto de quem instala; não há acesso contínuo; não há coleta destinada a informar um terceiro
sobre a rotina de alguém.

### 3.2 A inversão — e é o achado principal deste parecer

Os documentos formulam a consequência assim (Documento 2, §12.2):

> *"Se o parecer de classificação concluir que o produto se enquadra […], o candidato
> `WorkManager` puro está eliminado por política, não por precisão, porque não sustenta
> notificação persistente."*

**Essa formulação subestima a consequência, e a subestima na direção perigosa.**

Se o Modo Rua se enquadrasse como aplicativo de monitoramento, acrescentar notificação persistente
**não o salvaria**. A categoria aceitável é fechada em **pais→filhos** e **empresa→funcionário**,
e o Modo Rua é **adulto→si mesmo, com aviso a outro adulto**. A política é explícita: esses
aplicativos *"cannot be used to track anyone else (a spouse, for example) even with their
knowledge and permission, **regardless if persistent notification is displayed**"*.

> **Enquadrar-se não significa "precisar de notificação persistente". Significa que o produto
> não é publicável na Play.** O consentimento do contato — que é o núcleo do desenho — não é
> remédio: a política diz textualmente que não é.

Isso muda o que está em jogo no parecer. Ele não escolhe entre candidatos de temporização; ele
verifica se o produto existe.

### 3.3 Por que o produto fica fora — e o que isso torna inegociável

Os traços que mantêm o Modo Rua fora da categoria **não são detalhes de implementação**. São a
condição de publicabilidade, e passam a ser restrições de produto:

1. **O titular instala para si.** Qualquer fluxo em que A instala no aparelho de B para observar B
   move o produto para dentro da categoria — e a categoria o rejeita.
2. **Não existe acesso contínuo.** Concessão temporária, escopada, expirável, revogável, ativa
   **apenas durante protocolo autorizado**.
3. **O contato não tem poder sobre o protocolo**, não vê histórico completo e perde acesso no
   encerramento.
4. **Todo acesso é auditado e visível ao titular.**
5. **Nada é oculto:** ícone visível, sem modo invisível, sem coleta silenciosa.
6. **O marketing nunca se posiciona como vigilância.**
7. **Somente maiores de 18.** Sem exceção, e sem caminho de ativação futura dentro deste desenho.

### O plano familiar com menores não é uma funcionalidade futura deste produto

> **Decisão de documentação (fundador, 2026-07-27): o MVP é exclusivamente para maiores de 18
> anos. Plano familiar com menores é PRODUTO FUTURO SEPARADO, sujeito a nova análise de política,
> segurança, privacidade e classificação.**
>
> **Não tratar como simples feature futura ativável no mesmo desenho.**

O motivo é que ele muda camadas que não se ligam por flag. Ao menos nove:

classificação de público · política **Families** · requisitos de SDKs · consentimento parental ·
Data Safety · declaração **`child_monitoring`** · marketing · fluxo de conta · modelo jurídico.

A política oficial exige conformidade adicional para aplicativos desenvolvidos especificamente
para crianças. Um produto que nasce adulto→adulto e ganha um "modo familiar" não migra para essa
conformidade: ele passa a estar sujeito a ela **inteiro**, inclusive nas partes que não têm nada
a ver com o módulo novo.

É também a única hipótese em que a categoria de monitoramento **ajuda** em vez de matar — e
exatamente por isso ela pertence a outro produto, com outro parecer.

### 3.4 A política de Spyware — correção do fundador, e onde o risco realmente está

Este parecer, na primeira redação, tratava a política de Spyware como possível fonte de proibição
de compartilhamento de localização consentido entre adultos. **Isso está corrigido.**

> **A política de Spyware não proíbe, por si só, todo compartilhamento consentido de localização
> entre adultos.** Ela proíbe **coleta e transmissão inesperadas**, sem funcionalidade compatível,
> ou com comportamento que possa ser considerado espionagem. Exige aderência às políticas de
> dados, de divulgação e de consentimento.
> — correção do fundador, 2026-07-27, com fonte oficial.

**O risco real é de interpretação**, e está numa expressão: *"para fins de monitoramento"*. Não
basta que o desenho seja legítimo; ele precisa **demonstrar** que é. As nove propriedades abaixo
são o que faz a demonstração.

#### Controles de classificação e publicabilidade

> **Não são boas práticas. São controles**, na mesma acepção dos controles do Documento 3: sua
> ausência não degrada a experiência — **muda a classificação do produto e a possibilidade de
> publicá-lo.**

| # | Controle | Onde já está no corpus |
|---|---|---|
| 1 | **O dado pertence ao próprio titular** | Doc 3, §8.3; Doc 2, §4.7 (dados pré-conta são do titular local) |
| 2 | **A coleta é iniciada para benefício direto dele** | Doc 1, §6.1; Doc 2, §11 (a ativação é ato do titular) |
| 3 | **O contato não acompanha continuamente** | Doc 2, §24 — sem monitoramento permanente |
| 4 | **O acesso é temporário, escopado e revogável** | Doc 1, §18.2; Doc 2, §24; Doc 3, §22.2 |
| 5 | **O titular originou a sessão ou a emergência** | Doc 1, §7.1 (ativação manual no MVP); Doc 2, §10.2, linha 2 |
| 6 | **O contato não controla o aparelho** | Doc 2, §37.2 — **não existe canal de comando do servidor para o aparelho**; Doc 3, §22.2 |
| 7 | **Não existe modo oculto** | Doc 3, §23.2, §24.1; Doc 2, §41; núcleo §5 |
| 8 | **A interface informa claramente quando a localização está sendo coletada e compartilhada** | Doc 2, §11.1 (estado de cobertura); Doc 3, §34.4 (acessos visíveis ao titular); Doc 2, §13.3 (idade, precisão e fonte) |
| 9 | **O marketing não usa linguagem de rastreamento de terceiros** | Doc 1, §3.2, §23; Doc 3, §24.1 |

**Consequência prática de tratá-los como controles.** Uma proposta futura que enfraqueça qualquer
um deles — compartilhamento permanente "por conveniência", acesso do contato ao histórico,
localização coletada sem o titular ter ativado, sumário periódico enviado ao contato — não é uma
decisão de produto com trade-off de UX. **É uma mudança de classificação**, e como tal exige
reavaliação deste parecer antes de qualquer implementação. Quem escrever essa proposta encontra
esta seção antes de escrever o código.

O controle **6** merece nota: ele é sustentado por uma decisão arquitetural que já existe e cuja
motivação era outra. O Documento 2, §37.2 removeu comandos remotos porque *"quem emite comandos
controla o aparelho da vítima"*. O efeito colateral é que o produto **não tem como** dar controle
ao contato — a superfície não existe. Uma decisão tomada por segurança sustenta, de graça, um
controle de classificação.

---

## 4. Conclusão

> **PROVISÓRIA, pendente das verificações do §1.**
>
> **O Modo Rua, como especificado nos Documentos 1, 2 e 3, NÃO é um aplicativo de monitoramento
> na acepção da política da Google Play.** Ele é um aplicativo de segurança pessoal: o titular o
> instala para a própria proteção, e o terceiro recebe aviso e acesso temporário consentido
> durante uma emergência que o próprio titular originou.

**Precedente de mercado, como sinal e não como prova:** aplicativos de segurança pessoal com
temporizador de check-in, contatos de confiança e compartilhamento de localização em emergência
são distribuídos na Play — inclusive o **Personal Safety** do próprio Google, com *Emergency
Sharing* e *safety check*, e produtos de terceiros como **Noonlight** e **bSafe**, que têm
*safety timer check-ins*. Isso não substitui a verificação: é indício de que o padrão do Modo Rua
é tratado como segurança pessoal, não como monitoramento.

---

## 5. Consequências

### 5.1 Para o ADR-0007 — nenhum candidato é eliminado por esta política

> **`WorkManager` puro NÃO está eliminado por política.** Ele segue vivo e será decidido por
> **medição**, junto com os demais candidatos do Documento 2, §12.2.

A exigência de notificação persistente **da política de monitoramento** não incide. Permanecem em
pé, por outras razões e sem relação com este parecer:

- a notificação obrigatória de **serviço em primeiro plano**, se o ADR-0007 escolher um FGS —
  exigência do Android, não da política de monitoramento;
- a **notificação persistente da sessão** com tempo restante e ação de confirmação antecipada,
  que o produto adota por princípio antivigilância (Documento 3, §23.2; módulo 30, §6) e por
  necessidade de affordance — **escolha do produto, não imposição da loja**;
- a **permissão de notificação e o canal `check_in` como pré-condição de sessão** (Documento 2,
  §12.4), que existe porque a notificação é a única forma de pedir a confirmação.

### 5.2 Para a flag `isMonitoringTool` — posição de Fase 0 do fundador

> **Não declarar `isMonitoringTool` no Modo Rua, salvo se a revisão formal do Play Console
> determinar que o produto se enquadra obrigatoriamente nessa categoria.**
> — posição registrada pelo fundador em 2026-07-27.

**A flag não deve ser usada preventivamente.** A documentação oficial a apresenta como requisito
para aplicativos que **efetivamente são classificados** como ferramentas de monitoramento — não
como declaração defensiva de quem tem dúvida. E não há opção neutra: `other` encaminha o produto
para avaliação de categoria de isenção, o que é submeter-se ao julgamento em vez de evitá-lo.

**Regras operacionais que decorrem, e valem desde já:**

1. **não inserir a flag no `spike/` nem no manifesto-base;**
2. **não fechar o ADR definitivo antes das verificações** do §6;
3. **registrar a hipótese provisória** como *"não é ferramenta de monitoramento"*;
4. **manter a possibilidade de revisão** caso o formulário real ou a equipe da Play classifique o
   fluxo de outra forma.

Os três motivos que sustentam a posição, em ordem de peso:

1. **Declarar seria falso.** A flag existe para que a revisão da Play designe o aplicativo como
   aplicativo de monitoramento. O Modo Rua não é.
2. **Declarar seria contraproducente para a aprovação.** A flag põe o aplicativo numa categoria
   cujos únicos membros aceitáveis são pais→filhos e empresa→funcionário. O Modo Rua não é nenhum
   dos dois — a declaração convidaria à rejeição em vez de evitá-la.
3. **Declarar contradiria o produto.** A flag aciona *warning string* do Play Protect informando
   ao usuário que **há um aplicativo de monitoramento no aparelho**. Um produto cujo Documento 3
   trata prevenção de stalking como ameaça principal e proíbe posicionamento de vigilância não
   pode pedir ao sistema operacional que o anuncie como ferramenta de monitoramento.

**O ADR definitivo não é proposto agora, por instrução do fundador.** Módulo 50, §5: políticas de
loja só existem por **ADR completo**, e só o fundador aceita. A decisão sobre a flag e sobre a
divulgação na descrição é entregável nominal da Fase 0 (Documento 2, §34.6) e será fechada por ADR
**depois** das verificações do §6 — não antes.

### 5.3 Para a descrição da loja

A tensão declarada no §34.6 — promover a funcionalidade central de localização **e** divulgar
monitoramento **sem** se posicionar como vigilância — **dissolve-se**, se a conclusão se
confirmar: não havendo funcionalidade de monitoramento, não há o que divulgar sob essa política.
Permanece a exigência **de outra política**: promover de forma proeminente o conjunto de recursos
centrais que justificam a localização em segundo plano (Documento 2, §34.5) — que é assunto do
**ADR-0008**, não deste parecer.

---

## 6. O que precisa ser verificado antes de este parecer ser definitivo

| # | Pergunta exata | Onde | Quem |
|---|---|---|---|
| 1 | A redação vigente mantém *"the only acceptable monitoring apps"* limitada a pais→filhos e empresa→funcionário? | Play Console Help, answer/9888380 | fundador, via Play Console |
| 2 | A cláusula *"regardless if persistent notification is displayed"* está no texto vigente? | idem | idem |
| 3 | ~~A política de Spyware proíbe compartilhamento consentido entre adultos?~~ **RESPONDIDA** pelo fundador com fonte oficial: **não proíbe por si só** — proíbe coleta e transmissão inesperadas, sem funcionalidade compatível, ou com comportamento assimilável a espionagem. Resta confirmar que os nove controles do §3.4 **bastam** para afastar a leitura de "para fins de monitoramento" | Play Console Help, answer/14745000 | idem |
| 4 | O formulário real do Play Console **pergunta** sobre monitoramento? Em que termos? | Play Console, declarações de conteúdo | idem |
| 5 | A *warning string* do Play Protect é acionada pela flag, e com que texto ao usuário? | developers.google.com/android/play-protect/warning-strings | idem |
| 6 | Há orientação oficial sobre a categoria "segurança pessoal" que confirme o enquadramento alternativo? | Play Console Help | idem |

**Nenhuma dessas verificações exige aparelho.** Todas exigem a conta do Play Console — item #10 da
preparação da Fase 0, semana 1 do timebox.

**Se a verificação contrariar a conclusão**, o efeito não é ajustar o ADR-0007: é acionar a
condição de interrupção do Documento 1, §32 — *"a função principal não é permitida"* — porque,
como o §3.2 mostra, enquadrar-se não é um custo de notificação, é uma proibição.

---

## 7. Achado de documentação, aberto como issue

O Documento 2, §12.2 e §34.6 descrevem a consequência de o produto se enquadrar como sendo a
eliminação do candidato `WorkManager` puro. Pelas fontes consultadas, a consequência real é mais
grave: **o produto não seria publicável**, porque a categoria aceitável não admite adulto→adulto
nem com consentimento.

Não corrigi o Documento 2 — a correção depende das verificações do §6, e alterar o documento com
base em fonte secundária seria exatamente o que o módulo 30, §9 proíbe. Registrado como issue.
