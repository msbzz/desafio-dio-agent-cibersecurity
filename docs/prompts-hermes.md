# Prompts Utilizados no Hermes Agent

Este documento reúne os três prompts utilizados durante o laboratório para validar a configuração do **DIO Agent (GuardAI)**, executar a análise defensiva de vulnerabilidades e aplicar a correção com validação.

---

## 1. Teste de Configuração e Reconhecimento da Skill

Utilizado para confirmar que o Hermes carregou corretamente o `AGENTS.md`, a persona em `agent/persona.md` e o roteiro de `skills/vulnerability-analysis/SKILL.md` sem executar comandos prematuramente.

```text
Leia o arquivo AGENTS.md deste projeto, a persona em agent/persona.md e a skill skills/vulnerability-analysis/SKILL.md.

Confirme qual é sua função neste projeto, quando deve utilizar a skill de análise de vulnerabilidades e quais etapas deverá seguir.

Não execute comandos nem modifique arquivos nesta etapa.
```

---

## 2. Análise de Vulnerabilidade (Diagnóstico Estático)

Utilizado para solicitar a inspeção técnica do código de login (`agent/app.py`) seguindo as 7 seções padronizadas do `SKILL.md`, sem alterar o código original.

```text
Utilize a skill vulnerability-analysis para analisar o arquivo agent/app.py deste projeto.

Este é um laboratório educacional próprio e autorizado.

Verifique se o código de login apresenta vulnerabilidade de SQL Injection.

Identifique a linha vulnerável, explique tecnicamente o problema, avalie impacto, probabilidade e severidade e recomende uma correção.

Mostre como a consulta SQL deveria ser construída de maneira segura, mas não modifique nenhum arquivo.

Siga o procedimento definido no SKILL.md e apresente um relatório técnico.
```

---

## 3. Correção Autorizada e Validação

Utilizado após a análise inicial para autorizar a criação do backup didático (`agent/app_vulneravel.py`), a parametrização da query em `agent/app.py` e a validação em banco em memória.

```text
Com base na análise anterior, autorizo a correção da vulnerabilidade SQL Injection identificada em agent/app.py.

Utilize a skill vulnerability-analysis.

Antes de alterar o arquivo, crie uma cópia de segurança chamada agent/app_vulneravel.py.

Corrija a consulta utilizando parâmetros do sqlite3, preservando o comportamento original da função fazer_login.

Não modifique outros arquivos.

Após a alteração, apresente o código corrigido, explique as mudanças e informe quais testes ainda precisam ser realizados.
```
