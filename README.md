# GuardAI — Especialização do DIO Agent em Cibersegurança

**Projeto final — DIO | GuardAI: Agente Inteligente para Cibersegurança e Análise de Vulnerabilidades**

> **Documentação do projeto base:** [README_ORIGINAL.md](README_ORIGINAL.md)  
> **Repositório original da DIO:** https://github.com/digitalinnovationone/dio-agent

## 1. Objetivo

Especializar o DIO Agent, executado no **Hermes Agent**, para atuar como mentor de segurança da informação: compreender o contexto, identificar vulnerabilidades em código, avaliar impacto, probabilidade e severidade, orientar correções e validar os resultados. A especialização foi realizada por meio de arquivos Markdown, **sem treinamento de um novo modelo**.

O cenário escolhido foi uma função de login em **Python com SQLite**, contendo uma vulnerabilidade intencional de **SQL Injection**. A atividade ocorreu em laboratório próprio e autorizado.

## 2. O que foi desenvolvido

| Componente | Arquivo | Finalidade |
|---|---|---|
| Skill de análise | [`skills/vulnerability-analysis/SKILL.md`](skills/vulnerability-analysis/SKILL.md) | Roteiro para análise, classificação, correção e validação de vulnerabilidades |
| Guia da skill | [`skills/vulnerability-analysis/README.md`](skills/vulnerability-analysis/README.md) | Explicação da finalidade e utilização da habilidade |
| Conhecimento | [`agent/knowledge/security-basics.md`](agent/knowledge/security-basics.md) | Conceitos de vulnerabilidade, ameaça, risco, confidencialidade, integridade, disponibilidade e boas práticas |
| Persona | [`agent/persona.md`](agent/persona.md) | Especialização do mentor em segurança defensiva, preservando sua identidade educacional |
| Registro de habilidades | [`AGENTS.md`](AGENTS.md) | Inclusão da skill na tabela de habilidades e referência ao conhecimento de segurança |
| Código corrigido | [`agent/app.py`](agent/app.py) | Função de login após parametrização da consulta SQL |
| Código original vulnerável | [`agent/app_vulneravel.py`](agent/app_vulneravel.py) | Cópia para comparação didática; **não utilizar em produção** |

Os arquivos e caminhos acima descrevem a estrutura desenvolvida no laboratório. Ao publicar, confirme que todos estão incluídos no repositório.

## 3. Execução com Hermes Agent

O Hermes foi executado no **Windows 11**, por meio do Dashboard local, utilizando o diretório do projeto `dio-agent` como workspace. No teste relatado, a interface mostrou **Hermes Agent v0.23.1** e o modelo **`gpt-6.1-sol`**.

1. Instale e configure o Hermes conforme a [documentação oficial](https://hermes-agent.nousresearch.com/docs).
2. Abra o projeto `dio-agent` como diretório de trabalho da sessão (workspace).
3. Inicie o Hermes no terminal ou utilize seu Dashboard local, se disponível.
4. Confirme que o agente consegue ler `AGENTS.md`, `agent/persona.md` e `skills/vulnerability-analysis/SKILL.md`.
5. Solicite a análise de `agent/app_vulneravel.py`, com autorização explícita e sem modificar arquivos inicialmente.

**Exemplo de prompt para reproduzir a análise:**

```text
Utilize a skill vulnerability-analysis para analisar agent/app_vulneravel.py.
Este é um laboratório educacional próprio e autorizado.
Identifique possíveis vulnerabilidades de SQL Injection, apresente evidências
no código, avalie impacto, probabilidade e severidade e recomende uma correção.
Não altere nenhum arquivo nesta etapa. Siga o roteiro do SKILL.md.
```

> Para reproduzir o cenário **antes da correção**, analise `agent/app_vulneravel.py`. O arquivo `agent/app.py` já contém a versão corrigida.

## 4. Vulnerabilidade identificada

A função `fazer_login(usuario, senha)` montava a instrução SQL inserindo diretamente os parâmetros recebidos por meio de uma *f-string*:

```python
query = f"SELECT * FROM usuarios WHERE username = '{usuario}' AND password = '{senha}'"
cursor.execute(query)
```

**Causa:** entradas não confiáveis passam a integrar o texto SQL e podem modificar a condição de autenticação. O Hermes classificou a severidade como **alta**, considerando o potencial de contornar a verificação de credenciais. Essa classificação se refere ao risco técnico do trecho; a exposição externa da aplicação não foi demonstrada.

Na primeira análise, o Hermes identificou a falha por **inspeção estática** e não executou a aplicação. Na etapa seguinte, mediante autorização, informou ter realizado testes isolados com SQLite em memória.

## 5. Correção realizada pelo Hermes

O Hermes recebeu autorização para criar `agent/app_vulneravel.py` como backup e corrigir **somente** `agent/app.py`. A consulta passou a utilizar parâmetros vinculados pelo driver SQLite:

```python
query = "SELECT * FROM usuarios WHERE username = ? AND password = ?"
cursor.execute(query, (usuario, senha))
```

Assim, os argumentos são tratados como **dados**, sem alterar a estrutura da consulta. A assinatura da função e as mensagens de retorno foram preservadas. A correção da injeção **não resolve, por si só, questões adicionais**, como armazenamento seguro de senhas.

## 6. Evidências e validação

**Verificação visual no PowerShell:** foi conferido que `agent/app.py` contém a consulta parametrizada e `agent/app_vulneravel.py` preserva a consulta com *f-string*.

**Testes informados pelo Hermes:** o agente relatou **9 testes aprovados**, utilizando SQLite em memória, sem acessar o banco do projeto: credenciais válidas, senha incorreta, usuário inexistente, entradas vazias, injeção no usuário, injeção na senha, credenciais legítimas com aspas, texto semelhante a SQL como usuário válido e texto semelhante a SQL como senha válida. Também informou ter verificado o fechamento da conexão nos cenários testados.

Esses resultados são **relatos do agente**, não uma execução independente documentada neste README. Para fortalecer a evidência do portfólio, recomenda-se adicionar capturas da análise inicial, da correção, dos testes e da comparação entre os arquivos.

**Validações ainda pendentes:** integração com cópia controlada do esquema real do banco; fluxo completo de autenticação; falhas de banco indisponível ou tabela ausente; revisão de armazenamento de senhas e demais caminhos de login.

## 7. Melhorias em relação ao projeto base

A evolução escolhida foi uma **skill especializada em análise defensiva de vulnerabilidades**, integrada à persona e ao registro de skills do DIO Agent. O agente passou a seguir um fluxo consistente: **entender o contexto → identificar a falha → apresentar evidências → avaliar risco → orientar correção → validar**. O teste prático demonstrou o uso desse roteiro na análise e correção de SQL Injection.

## 8. Organização da entrega

```text
dio-agent/
├── README.md                         # Esta documentação do projeto final
├── README_ORIGINAL.md                # Documentação original da DIO
├── AGENTS.md                         # Registro da nova skill e conhecimento
├── agent/
│   ├── persona.md                    # Especialização em segurança
│   ├── app.py                        # Login corrigido
│   ├── app_vulneravel.py             # Exemplo vulnerável (somente laboratório)
│   └── knowledge/
│       └── security-basics.md        # Fundamentos de segurança
├── skills/
│   └── vulnerability-analysis/
│       ├── SKILL.md                  # Procedimento do agente
│       └── README.md                 # Guia para pessoas
└── ...                               # Demais arquivos originais preservados
```

## 9. Publicação e evidências

A entrega da DIO prevê um **repositório público no GitHub**, contendo o projeto base com as modificações e este README. Antes de enviar o link:

- Verifique que todos os caminhos documentados existem e que a skill consta da tabela de `AGENTS.md`.
- Acrescente, se possível, capturas do Dashboard com a leitura das instruções, a identificação da falha e a correção/validação.
- Confirme que o repositório está público e que o link enviado aponta para **o repositório**, não para um arquivo isolado.
- Não publique senhas reais, tokens, chaves, bancos com dados pessoais ou configurações privadas do Hermes.

## 10. Conclusão

O exercício demonstrou a especialização do DIO Agent por **skill, conhecimento, persona e AGENTS.md**, com execução pelo Hermes. O agente identificou uma consulta vulnerável, recomendou parametrização, aplicou a alteração após autorização e relatou testes isolados bem-sucedidos. A comprovação completa de segurança do sistema exige testes adicionais de integração e revisão de outros componentes.

---

**Créditos:** projeto base [DIO Agent — Digital Innovation One](https://github.com/digitalinnovationone/dio-agent). Esta documentação descreve a especialização realizada para o desafio final.
