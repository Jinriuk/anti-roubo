# `docs/evidencias/` — evidências de teste manual e de campo

**Regra que cria este diretório:** *"Uma fase não termina quando o código existe. Termina quando
o comportamento foi comprovado."* e *"Sem evidência, não há aceite. Sem aceite, não há avanço."*
(Documento 5, §1 e conclusão.) *"Evidências de teste manual arquivadas em `docs/evidencias/` com
data e versão"* (módulo 50, §9).

## ⚠️ O template não está disponível

O template de evidência é **canônico e vive no núcleo §9**. O Documento 5, §6 e §7 e os módulos
40 e 50 declaram explicitamente **não manter template próprio** — e o núcleo não está no
repositório (`docs/agentes/00-nucleo.AUSENTE.md`).

Nenhum arquivo deste diretório deve ser preenchido com um template improvisado: o formato de
registro é fonte, e inventá-lo aqui seria a mesma falha que os documentos existem para impedir.

`docs/fase-0/metodo-de-medicao.md` define o **conteúdo** de cada medição — o que é medido, com
que instrumento, com que n, com que saída. Falta o **formato**.

## Dois níveis de evidência

Segundo o núcleo §3.1, citado pelo módulo 40 §5 e pelo módulo 50 §3 e §4:

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
