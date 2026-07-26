# `docs/fabricantes/` — comportamento observado por marca

Um arquivo por marca: `docs/fabricantes/<marca>.md` (`samsung.md`, `motorola.md`,
`xiaomi.md`, …).

**Regra que cria este diretório:** *"Toda hipótese de retomada é verificada em aparelho e
documentada em `docs/fabricantes/<marca>.md`, com aparelho e versão testados. Retomada
impossível gera aviso honesto ao usuário e evento ao servidor, que passa a registrar perda de
cobertura. Nunca silêncio."* (Documento 2, §35; módulo 10, §6 e §11.)

Adaptações por fabricante ficam isoladas em `core:*`, nunca espalhadas por features. **O usuário
nunca é instruído a desativar toda a proteção de bateria.**

## O que cada arquivo registra

Por aparelho e por versão testada:

- identificação: marca, modelo, versão do Android e nível de API, build do fabricante
  (One UI / HyperOS / MIUI / Realme UI, com versão);
- **retomada após reboot** — possível / parcial / não comprovada, por conjunto de permissões;
- **sobrevivência a force-stop** e ao **gerenciador de tarefas do fabricante** — são dois
  ensaios distintos, com linhas separadas;
- comportamento do gerenciador de bateria e efeito da isenção de otimização;
- comportamento de notificação com Não Perturbe e com tela bloqueada;
- latência de transição de cerca de proximidade, e se a cerca sobrevive ao reinício;
- efeito da invalidação de chave após novo cadastro biométrico, após restauração e em chave que
  aceita `DEVICE_CREDENTIAL`;
- qualquer divergência entre o comportamento documentado pela plataforma e o observado —
  com a fonte oficial citada ao lado do observado.

## Regras

- Nada aqui é escrito por inferência. Só entra o que foi **observado em aparelho**, por executor
  humano identificado.
- Nenhum dado pessoal real, nenhuma coordenada real, nenhuma captura com conteúdo real.
- Divergência observada que contrarie afirmação de plataforma nos documentos **vira issue**, não
  correção silenciosa.

Vazio até a Fase 0 produzir a primeira observação.
