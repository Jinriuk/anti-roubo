# `docs/evidencias/` — evidências de teste manual e de campo

**Regra que cria este diretório:** *"Uma fase não termina quando o código existe. Termina quando
o comportamento foi comprovado."* e *"Sem evidência, não há aceite. Sem aceite, não há avanço."*
(Documento 5, §1 e conclusão.) *"Evidências de teste manual arquivadas em `docs/evidencias/` com
data e versão"* (módulo 50, §9).

## Template

**Canônico: núcleo §9**, em `docs/agentes/00-nucleo.md`. O Documento 5, §6 e §7 e os módulos 40
e 50 declaram explicitamente não manter template próprio, e este diretório também não mantém.
Colar preenchido na PR e arquivar aqui.

Campos que o template exige e que costumam ser omitidos: **status declarado**
(IMPLEMENTADA | NÃO IMPLEMENTADA), **resultado esperado e resultado observado em campos
separados**, divergências, limitações conhecidas e decisão.

> **Executor:** o campo é literal no template — *"humano identificado; **o agente não executa
> teste físico e não preenche resultado observado**"*. O agente escreve o método, o instrumento
> e todos os testes automatizáveis; o resultado observado é de quem observou.

`docs/fase-0/metodo-de-medicao.md` define o **conteúdo** de cada medição da Fase 0 — o que é
medido, com que instrumento, com que n e com que saída.

## Dois níveis de evidência

Núcleo §3.1:

- **automatizada, por PR** — testes que provam o comportamento;
- **de campo, por marco de fase** e sempre que a mudança **alterar comportamento observável** de
  background, localização, notificação, Keystore, boot ou adaptação de fabricante.

O rótulo `aguardando-evidencia-campo` é aplicado **pelo CI**, a partir dos caminhos alterados —
não pelo julgamento do autor. O humano só pode **removê-lo**, com justificativa registrada na PR.

## Matriz de evidência mínima por área

Documento 5, §6 — origem canônica, não copiada aqui. Fases com exigência de **aparelho físico**:
0, 1, 3, 4, 6 e 7.

## Regras de registro

- Data e versão do aplicativo em todo arquivo; executor humano identificado.
- **Dados sintéticos, sempre.** Nenhum dado pessoal real em evidência, fixture ou captura de
  tela — a proibição do módulo 30 §9 vale aqui integralmente.
- Toda saída percentual declara **n**. Percentual sem denominador é proibido.
- **Evidência inventada é falha crítica.** Célula sem coleta fica declarada vazia; não é
  estimada, não é interpolada e não vira "provisório".

Vazio até a Fase 0 produzir a primeira medição.
