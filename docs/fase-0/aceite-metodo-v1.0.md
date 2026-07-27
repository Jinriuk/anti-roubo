# Aceite formal — Método de medição da Fase 0, versão 1.0

Registro de congelamento, exigido pelo §0 do próprio método.
**Este documento não é alterável.** Aceite de versão posterior gera registro próprio.

---

## Os oito campos

| Campo | Valor |
|---|---|
| **Caminho do arquivo** | `docs/fase-0/metodo-de-medicao.md` |
| **Versão** | **1.0** |
| **Hash** | `sha256:e3367d8448321301a7f554c804b1c0ba512697f7a6cd30f72b12c46bd485afba` |
| **Commit de congelamento** | `6b334d6e249f9a5abe78fbc14ecc268666226e5a` |
| **Data e hora** | 2026-07-27 (UTC) |
| **Aprovador humano** | fundador do projeto |
| **Medições abrangidas** | **M1 a M15** — as quinze da tabela do Documento 5, Fase 0 |
| **Issues e ADRs relacionados** | issues #9 (timebox), #10 (este aceite), #14 (build da Play na M6), #3 e #6 (insumos de M7 e M11) · ADRs **0005-A**, **0005-B**, **0007**, **0008**, **0011**, **0012**, e a revisão do **0002** |

**Verificação:**

```bash
sha256sum docs/fase-0/metodo-de-medicao.md
# deve devolver e3367d8448321301a7f554c804b1c0ba512697f7a6cd30f72b12c46bd485afba
```

Divergência entre este hash e o do arquivo em uso significa que o método foi alterado após o
congelamento sem passar por versão nova. Nesse caso, **toda coleta feita sob a versão divergente
é evidência inválida** até que a alteração seja reconciliada pelo §0.

## Termos da aprovação

> **MÉTODO v1.0 APROVADO, CONDICIONADO À CORREÇÃO DA INTERPRETAÇÃO DO LIMITE SUPERIOR DO p99.**

A correção foi aplicada **antes** do congelamento, no commit acima. Conforme declarado na
aprovação, ela **não alterou**: estimador · amostra · procedimento · critérios de invalidação ·
instrumentos · variáveis.

### O que a correção mudou

**Obrigatória — §4.4.** O máximo da amostra deixou de ser tratado como teto do p99. O texto passa
a declarar que ele **não é teto verdadeiro do p99 nem limite superior garantido da latência da
plataforma** — é apenas o limite observável que a amostra conseguiu produzir, e a plataforma pode
apresentar atrasos maiores em uso futuro. A derivação da `margem_de_rede` foi reescrita como
**proposta inicial conservadora**, que recebe fator ou parcela adicional de segurança no
ADR-0005-B.

**Aprovadas junto:** §4.3 (rejeição da interpolação reenquadrada como decisão metodológica deste
projeto, mais o alerta de que ferramentas interpolam por padrão) · §4.5 (execução invalidada
permanece no conjunto de auditoria; taxa elevada de invalidação é resultado) · §0 (efeito de
alteração decidido por "afeta comparabilidade ou interpretação"; reaproveitamento justificado
**antes** de combinar dados) · §6.0 (seis formas de reprovação estrutural) · §7.1 (decomposição da
`margem_de_rede` e as quatro margens distintas) · §7.2 (dimensões do ADR-0012 pré-registradas).

## Pendência declarada no ato do aceite

O Documento 5 manda medir consumo **apenas no nível `economico`**. Os níveis `elevado` e
`emergencial` não são medidos nesta fase, e a dimensão *"consumo por nível de coleta"* do
ADR-0012 fica **parcialmente coberta**. Corrigir exigiria alterar o escopo do Documento 5, o que
esta aprovação não autoriza.

Antes do ADR-0012, uma das duas: acrescentar os dois níveis por **versão nova do método** — que é
acréscimo e, pelo §0, não invalida nada já coletado —, ou o **ADR-0012 declarar a dimensão como
não medida** e decidir sem ela.

## Efeito

A partir deste registro, o método está **VIGENTE** e **congelado**. A coleta pode começar.

Toda evidência arquivada em `docs/evidencias/` declara a versão do método sob a qual foi
coletada. O revisor confere se a versão declarada é anterior ao início da coleta registrado na
própria evidência.
