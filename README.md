# GuardAI — Especialização do DIO Agent em Cibersegurança

**Projeto Final — DIO | GuardAI: Agente Inteligente para Cibersegurança e Análise de Vulnerabilidades**

> **Documentação do projeto base:** [README_ORIGINAL.md](README_ORIGINAL.md)  
> **Repositório original da DIO:** https://github.com/digitalinnovationone/dio-agent  
> **Repositório deste projeto:** https://github.com/msbzz/desafio-dio-agent-cibersecurity

---

## 1. Objetivo e Especialização

Este projeto transforma o **DIO Agent** em um **mentor de cibersegurança e analista de AppSec** (**GuardAI**), executado no harness **Hermes Agent**. Em vez de entregar respostas diretas ou comandos soltos sem contexto, o agente especializado segue um roteiro profissional de segurança defensiva:

1. **Entende o contexto** e confirma que o ambiente é próprio/autorizado antes de agir;
2. **Identifica vulnerabilidades** com base em evidências técnicas no código;
3. **Avalia riscos** classificando impacto (Tríade CIA), probabilidade e severidade;
4. **Orienta correções defensivas** explicando o porquê de cada medida;
5. **Valida a mitigação** com testes funcionais e de segurança e aponta riscos residuais.

Toda a especialização foi construída utilizando exclusivamente **arquivos Markdown** (`SKILL.md`, `security-basics.md`, `persona.md` e `AGENTS.md`), **sem treinar ou alterar pesos de nenhum modelo**. O cenário prático de laboratório consiste na análise, correção e validação de uma vulnerabilidade de **SQL Injection** em uma função de autenticação em **Python + SQLite**.

---

## 2. Arquivos Criados e Alterados

A tabela abaixo detalha todos os componentes adicionados ou modificados em relação ao repositório base da DIO:

| Componente | Arquivo | Tipo | Finalidade |
|---|---|---|---|
| **Skill de Análise** | [`skills/vulnerability-analysis/SKILL.md`](skills/vulnerability-analysis/SKILL.md) | Criado | Roteiro estruturado em 7 etapas para análise, classificação de risco, correção e validação de vulnerabilidades (com frontmatter `name` e `description`) |
| **Guia da Skill** | [`skills/vulnerability-analysis/README.md`](skills/vulnerability-analysis/README.md) | Criado | Documentação voltada para pessoas explicando o objetivo, funcionamento e exemplo de uso da skill |
| **Base de Conhecimento** | [`agent/knowledge/security-basics.md`](agent/knowledge/security-basics.md) | Criado | Fundamentos de vulnerabilidade, ameaça, risco, tríade CIA (Confidencialidade, Integridade e Disponibilidade), SQL Injection e boas práticas defensivas |
| **Persona do Agente** | [`agent/persona.md`](agent/persona.md) | Alterado | Adição da seção *Especialização em Segurança da Informação*, definindo a postura de mentor e analista defensivo |
| **Registro do Agente** | [`AGENTS.md`](AGENTS.md) | Alterado | Inclusão de `security-basics.md` na base de conhecimento e registro da skill `vulnerability-analysis` na tabela de skills |
| **Código Corrigido** | [`agent/app.py`](agent/app.py) | Criado/Corrigido | Função `fazer_login` corrigida pelo Hermes com consulta SQL parametrizada (`?`) |
| **Código Vulnerável** | [`agent/app_vulneravel.py`](agent/app_vulneravel.py) | Criado | Cópia de segurança da versão original com *f-string* para comparação didática (**uso exclusivo em laboratório**) |
| **Prompts do Laboratório** | [`docs/prompts-hermes.md`](docs/prompts-hermes.md) | Criado | Registro dos 3 prompts enviados ao Hermes (verificação de configuração, análise de vulnerabilidade e correção autorizada) |
| **Relatório de Análise** | [`docs/relatorio-analise-sqli.md`](docs/relatorio-analise-sqli.md) | Criado | Relatório técnico completo gerado pelo Hermes na etapa de diagnóstico estático |
| **Relatório de Correção** | [`docs/relatorio-correcao-sqli.md`](docs/relatorio-correcao-sqli.md) | Criado | Relatório técnico gerado pelo Hermes após parametrizar a consulta e executar 9 testes em memória |

---

## 3. Como Rodar o Agente no Harness (Hermes Agent)

O laboratório foi executado no **Windows 11**, utilizando o **Hermes Agent v0.23.1** (modelo `gpt-6.1-sol`) tanto via CLI no PowerShell quanto via Dashboard Web local (`http://127.0.0.1:9119`).

### Passo a passo para reprodução

1. Instale e configure o **Hermes Agent** seguindo a [documentação oficial](https://hermes-agent.nousresearch.com/docs).
2. Clone este repositório e acesse a **pasta raiz do projeto** no terminal (importante para que o Hermes localize `AGENTS.md` automaticamente):
   ```powershell
   git clone https://github.com/msbzz/desafio-dio-agent-cibersecurity.git
   cd desafio-dio-agent-cibersecurity
   ```
3. Crie ou verifique a estrutura da skill (caso esteja reproduzindo do zero):
   ```powershell
   New-Item -ItemType Directory -Path ".\skills\vulnerability-analysis"
   ```
   ![Criação do diretório da skill](images/criando%20nova%20skill.png)

4. Inicie o Hermes no terminal dentro da pasta do projeto:
   ```powershell
   hermes
   ```
   ![Ativando o Hermes no PowerShell](images/01_ativando%20hermes.png)
   ![Hermes Agent inicializado com skills carregadas](images/02_hermes%20ativado.png)

5. *(Opcional)* Para utilizar a interface gráfica no navegador, execute:
   ```powershell
   hermes dashboard
   ```
   ![Hermes Agent Dashboard Web](images/hermes%20dashboard.png)

---

## 4. Prompts Utilizados e Validação da Configuração

Todos os prompts estão documentados em [`docs/prompts-hermes.md`](docs/prompts-hermes.md).

### Etapa 1 — Confirmando a leitura de `AGENTS.md`, `persona.md` e `SKILL.md`

Antes de analisar o código, foi enviado um prompt de verificação para garantir que o agente compreendeu seu papel de mentor de cibersegurança, os gatilhos de ativação da skill e as etapas obrigatórias:

```text
Leia o arquivo AGENTS.md deste projeto, a persona em agent/persona.md e a skill skills/vulnerability-analysis/SKILL.md.

Confirme qual é sua função neste projeto, quando deve utilizar a skill de análise de vulnerabilidades e quais etapas deverá seguir.

Não execute comandos nem modifique arquivos nesta etapa.
```

O Hermes leu os três arquivos via `Read File` e confirmou todas as 6 etapas operacionais e os limites éticos/defensivos:

![Confirmação da configuração do Hermes Agent](images/confirmacao%20config%20hermes.png)

---

## 5. Análise da Vulnerabilidade (Diagnóstico pelo Hermes)

### Prompt enviado para análise

```text
Utilize a skill vulnerability-analysis para analisar o arquivo agent/app.py deste projeto.

Este é um laboratório educacional próprio e autorizado.

Verifique se o código de login apresenta vulnerabilidade de SQL Injection.

Identifique a linha vulnerável, explique tecnicamente o problema, avalie impacto, probabilidade e severidade e recomende uma correção.

Mostre como a consulta SQL deveria ser construída de maneira segura, mas não modifique nenhum arquivo.

Siga o procedimento definido no SKILL.md e apresente um relatório técnico.
```

> 📄 **Relatório completo retornado pelo agente:** [`docs/relatorio-analise-sqli.md`](docs/relatorio-analise-sqli.md)

### Síntese do diagnóstico técnico

O Hermes seguiu exatamente a estrutura exigida no item `## 6. Formato da resposta` do [`SKILL.md`](skills/vulnerability-analysis/SKILL.md):

1. **Contexto analisado:** Arquivo `agent/app.py`, função `fazer_login(usuario, senha)` (linha 4), utilizando Python + `sqlite3` em ambiente de laboratório autorizado, analisado por inspeção estática sem modificar arquivos.
2. **Vulnerabilidade identificada:** **SQL Injection** na linha 8 (construção da query com *f-string*) e execução direta na linha 10.
3. **Evidências técnicas:**
   ```python
   # Construção vulnerável — linha 8
   query = f"SELECT * FROM usuarios WHERE username = '{usuario}' AND password = '{senha}'"
   # Execução sem parâmetros vinculados — linha 10
   cursor.execute(query)
   ```
   Como as entradas não são isoladas da instrução SQL, um atacante pode injetar delimitadores (`'`) e expressões booleanas/comentários (ex.: `' OR '1'='1` ou `admin' --`) para alterar a lógica do `WHERE` e autenticar sem conhecer a senha. No **MITRE ATT&CK**, técnicas de exploração de aplicações voltadas ao acesso inicial enquadram-se em **T1190 — Exploit Public-Facing Application** (e uso de contas válidas/contorno de autenticação relacionável a **T1078 — Valid Accounts**).
4. **Impacto, probabilidade e severidade:**

   | Critério | Avaliação do Hermes |
   |---|---|
   | **Impacto principal** | Potencial contorno da verificação de credenciais realizada pela função (`"Acesso Permitido!"`). |
   | **Confidencialidade** | Acesso indevido aos recursos liberados após o login. |
   | **Integridade e Disponibilidade** | Não há evidência suficiente apenas neste trecho para afirmar alteração/exclusão de dados ou queda de disponibilidade. |
   | **Probabilidade** | **Alta**, caso entradas não confiáveis alcancem a função, pois os dois argumentos são concatenados sem sanitização ou parametrização. |
   | **Severidade** | **Alta** (falha na decisão de login sem exigir autenticação prévia; não classificada como crítica por não haver evidência de exposição externa ou privilégios do banco no arquivo isolado). |

5. **Recomendações complementares destacadas pelo agente:** Além da consulta parametrizada, o Hermes recomendou revisar o armazenamento de senhas em texto puro (sugerindo hash com **Argon2id**, conforme `security-basics.md`), validar entradas, aplicar menor privilégio no arquivo SQLite, tratar exceções sem expor erros internos e garantir o fechamento da conexão em caso de falha.

---

## 6. Correção Aplicada e Validação

### Prompt enviado para correção e testes

```text
Com base na análise anterior, autorizo a correção da vulnerabilidade SQL Injection identificada em agent/app.py.

Utilize a skill vulnerability-analysis.

Antes de alterar o arquivo, crie uma cópia de segurança chamada agent/app_vulneravel.py.

Corrija a consulta utilizando parâmetros do sqlite3, preservando o comportamento original da função fazer_login.

Não modifique outros arquivos.

Após a alteração, apresente o código corrigido, explique as mudanças e informe quais testes ainda precisam ser realizados.
```

> 📄 **Relatório completo da correção retornado pelo agente:** [`docs/relatorio-correcao-sqli.md`](docs/relatorio-correcao-sqli.md)

### Comparação: Antes (`agent/app_vulneravel.py`) vs. Depois (`agent/app.py`)

**Antes (Vulnerável):**
```python
query = f"SELECT * FROM usuarios WHERE username = '{usuario}' AND password = '{senha}'"
cursor.execute(query)
```

**Depois (Corrigido com Consulta Parametrizada):**
```python
query = "SELECT * FROM usuarios WHERE username = ? AND password = ?"
cursor.execute(query, (usuario, senha))
```

Com os placeholders `?` e a tupla `(usuario, senha)` passada como segundo argumento de `cursor.execute()`, o driver `sqlite3` vincula os valores estritamente como **dados literais**, impedindo que aspas ou comandos SQL modifiquem a árvore sintática da consulta.

### Evidência no Terminal (PowerShell)

Verificação lado a lado de `agent/app.py` (corrigido) e `agent/app_vulneravel.py` (backup didático):

![Comparação no PowerShell entre o código corrigido e o código vulnerável](images/verifucar%20arquivo%20corrijido%20hermes.png)

### Bateria de 9 Testes Executada pelo Hermes (SQLite em Memória)

Durante a validação, o Hermes criou um banco SQLite isolado em memória (`:memory:`) para reproduzir o ataque antes da correção (confirmando que a injeção retornava `"Acesso Permitido!"`) e validar o bloqueio após a correção. Todos os **9 cenários de teste** passaram:

1. ✅ Credenciais válidas (`"Acesso Permitido!"`).
2. ✅ Senha incorreta (`"Acesso Negado."`).
3. ✅ Usuário inexistente (`"Acesso Negado."`).
4. ✅ Entradas vazias (`"Acesso Negado."`).
5. ✅ Tentativa de SQL Injection no campo `usuario` (`"Acesso Negado."`).
6. ✅ Tentativa de SQL Injection no campo `senha` (`"Acesso Negado."`).
7. ✅ Credenciais legítimas contendo aspas simples.
8. ✅ Texto semelhante a comando SQL utilizado como nome de usuário literal válido.
9. ✅ Texto semelhante a comando SQL utilizado como senha literal válida.

### Riscos Residuais e Próximos Passos Apontados pelo Agente

Seguindo a diretriz do `SKILL.md` de *"não declarar o sistema seguro sem evidências suficientes"*, o agente listou as validações adicionais necessárias antes de produção:
- Teste de integração com cópia controlada do banco e esquema real;
- Revisão do armazenamento de senhas (substituir comparação direta de senha por hash seguro como **Argon2id**);
- Tratamento de erros de banco indisponível/tabela ausente e uso de gerenciador de contexto (`with`) para fechamento seguro da conexão.

---

## 7. Melhoria Escolhida em Relação ao Projeto Base

Conforme as sugestões da seção **"Ideias para Evoluir"** do desafio:

- **Padronização estrita do relatório de análise e avaliação de risco:** A skill `vulnerability-analysis` foi projetada para exigir um formato de resposta fixo em 7 seções obrigatórias (*Contexto analisado → Vulnerabilidade identificada → Evidências técnicas → Impacto, probabilidade e severidade → Correção recomendada → Procedimento de validação → Conclusão*), permitindo comparar casos de forma consistente.
- **Governança e segurança operacional no fluxo do agente:** Foram incluídas regras explícitas no `SKILL.md` que obrigam o agente a confirmar autorização do ambiente, distinguir falhas confirmadas de suspeitas, solicitar permissão antes de modificar arquivos, exigir backup prévio (`app_vulneravel.py`) e apontar riscos residuais após a correção.

---

## 8. Estrutura do Repositório

```text
desafio-dio-agent-cibersecurity/
├── README.md                                  # Documentação principal do desafio final
├── README_ORIGINAL.md                         # Documentação original do repositório base da DIO
├── AGENTS.md                                  # Fonte única de verdade do agente (com nova skill e knowledge)
├── CLAUDE.md                                  # Redirecionamento padrão para AGENTS.md
├── agent/
│   ├── persona.md                             # Persona do mentor + Especialização em Segurança da Informação
│   ├── app.py                                 # Código de login corrigido (consulta parametrizada)
│   ├── app_vulneravel.py                      # Código original vulnerável (backup didático de laboratório)
│   └── knowledge/
│       ├── dio-platform.md                    # Conhecimento original sobre a DIO
│       ├── learning-experiences.md            # Experiências educacionais da DIO
│       └── security-basics.md                 # [NOVO] Fundamentos de cibersegurança, Tríade CIA e SQLi
├── skills/
│   ├── README.md                              # Guia geral de skills
│   ├── vulnerability-analysis/                # [NOVA SKILL] Especialização em análise de vulnerabilidades
│   │   ├── SKILL.md                           # Roteiro passo a passo seguido pelo agente
│   │   └── README.md                          # Explicação da skill para estudantes e desenvolvedores
│   ├── ai-builder-path/
│   ├── explain-concept/
│   ├── portfolio-review/
│   ├── study-plan/
│   ├── unblock-challenge/
│   └── web-search/
├── docs/
│   ├── prompts-hermes.md                      # [NOVO] Prompts utilizados nas 3 etapas do laboratório
│   ├── relatorio-analise-sqli.md              # [NOVO] Relatório completo da análise estática pelo Hermes
│   ├── relatorio-correcao-sqli.md             # [NOVO] Relatório completo da correção e testes pelo Hermes
│   ├── glossary.md                            # Glossário original do projeto
│   └── youcom-mcp.md                          # Documentação complementar original
└── images/                                    # Capturas de tela e evidências de execução no Hermes
    ├── criando nova skill.png
    ├── 01_ativando hermes.png
    ├── 02_hermes ativado.png
    ├── hermes dashboard.png
    ├── confirmacao config hermes.png
    └── verifucar arquivo corrijido hermes.png
```

---

## 9. Créditos

- **Projeto Base:** [DIO Agent — Digital Innovation One](https://github.com/digitalinnovationone/dio-agent)
- **Harness utilizado:** [Hermes Agent — Nous Research](https://hermes-agent.nousresearch.com/docs)
