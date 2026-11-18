# Relatório Técnico — Análise de SQL Injection no Login

> Relatório gerado pelo **Hermes Agent** utilizando a skill `vulnerability-analysis` durante a etapa de diagnóstico estático.

## 1. Contexto analisado

- **Arquivo:** `agent/app.py` (preservado posteriormente como `agent/app_vulneravel.py`).
- **Função:** `fazer_login(usuario, senha)`, definida na linha 4.
- **Tecnologias identificadas:** Python e SQLite, por meio do módulo `sqlite3`.
- **Ambiente:** laboratório próprio e autorizado, conforme confirmação do estudante.
- **Método:** análise estática do código, seguindo a skill `vulnerability-analysis` e os fundamentos de `agent/knowledge/security-basics.md`.

**Nenhum arquivo foi modificado nesta etapa. A aplicação não foi executada nem foram realizados testes contra o banco de dados.**

## 2. Vulnerabilidade identificada

**O código apresenta SQL Injection na construção da consulta de login, em `agent/app.py:8`.**

Os parâmetros `usuario` e `senha` são interpolados diretamente em uma instrução SQL. A consulta resultante é executada sem parâmetros separados na linha 10.

Essa construção permite que entradas especialmente elaboradas sejam interpretadas como parte da linguagem SQL, em vez de apenas dados. A documentação oficial do `sqlite3` alerta para esse problema ao montar consultas com operações de strings ([docs.python.org](https://docs.python.org/3/library/sqlite3.html)).

**Estado do achado:** vulnerabilidade identificada por inspeção do código; exploração em execução não demonstrada.

## 3. Evidências técnicas

### Construção vulnerável — linha 8

```python
query = f"SELECT * FROM usuarios WHERE username = '{usuario}' AND password = '{senha}'"
```

### Execução — linha 10

```python
cursor.execute(query)
```

O fluxo observado é:

1. `usuario` e `senha` chegam como argumentos da função.
2. A f-string insere esses valores entre aspas simples no texto SQL.
3. `cursor.execute(query)` recebe apenas a instrução já montada, sem vinculação separada dos valores.
4. Nas linhas 11 e 14–15, qualquer registro retornado leva à mensagem `"Acesso Permitido!"`.

**Problema técnico:** as aspas escritas na f-string não garantem isolamento das entradas. Uma entrada contendo delimitadores e expressões SQL pode encerrar um valor textual e alterar a condição do `WHERE`. Esse é o mecanismo de injeção descrito pela documentação do módulo.

**Consequência inferida do código:** se a condição modificada retornar um registro, a função poderá informar acesso permitido sem que as credenciais originais correspondam a um usuário válido. Isso depende, entre outros fatores, de haver registros e de o chamador permitir o controle desses argumentos.

## 4. Impacto, probabilidade e severidade

| Critério | Avaliação |
|---|---|
| **Impacto principal** | Potencial contorno da verificação de credenciais realizada pela função. |
| **Confidencialidade** | Pode haver acesso indevido ao que o restante da aplicação liberar após o login. Esse alcance não aparece no arquivo analisado. |
| **Integridade e disponibilidade** | Não há evidência suficiente neste trecho para afirmar alteração ou exclusão de dados, nem comprometimento da disponibilidade. |
| **Probabilidade** | **Alta, caso entradas não confiáveis alcancem a função:** os dois argumentos são inseridos diretamente na consulta, sem parametrização. A exposição real da aplicação não foi verificada. |
| **Severidade** | **Alta**, considerando o potencial de contornar a autenticação. |
| **Justificativa** | A falha está na decisão de login e não exige autenticação prévia dentro da própria função. O alcance externo, os privilégios e os recursos protegidos não foram demonstrados, portanto não há base suficiente para classificá-la como crítica. |

A severidade descreve o **potencial técnico da falha**; não significa que o laboratório tenha sofrido uma exploração.

## 5. Correção recomendada

### Correção direta: consulta parametrizada

A consulta deve ter estrutura fixa, com os valores passados separadamente ao driver:

```python
query = "SELECT * FROM usuarios WHERE username = ? AND password = ?"
cursor.execute(query, (usuario, senha))
```

No `sqlite3`, os marcadores `?` representam valores vinculados pelo driver. **Não devem ser colocados entre aspas nem substituídos por f-strings, concatenação ou formatação manual.** Essa é a forma de parametrização documentada para separar dados da estrutura SQL.

Esse exemplo corrige **a montagem da consulta**, mas mantém a lógica atual de comparação da senha.

### Recomendações complementares

Conforme os fundamentos de segurança do projeto:

- **Revisar o armazenamento de senhas:** a comparação direta com `password` merece investigação. O arquivo não mostra cadastro ou armazenamento, portanto não permite confirmar como as senhas são persistidas. A orientação da base do projeto é utilizar um algoritmo apropriado, como Argon2id.
- **Validar entradas:** definir tipos e limites coerentes como defesa complementar, sem tratar isso como substituto da parametrização.
- **Aplicar menor privilégio:** restringir as permissões do processo sobre o arquivo SQLite.
- **Tratar erros com segurança:** não expor consultas, credenciais ou detalhes internos ao usuário.
- **Garantir fechamento da conexão:** revisar o fluxo para liberar a conexão também quando ocorrer uma exceção.

## 6. Procedimento de validação

Após uma correção autorizada, recomenda-se:

1. **Reexaminar o código:** confirmar que ambos os argumentos são vinculados por parâmetros e que não há interpolação equivalente em outros caminhos de login.
2. **Usar banco descartável com dados fictícios:** manter os testes isolados do banco real do laboratório.
3. **Executar testes funcionais:**
   - credenciais corretas devem permitir acesso;
   - senha incorreta deve negar acesso;
   - usuário inexistente deve negar acesso.
4. **Executar testes de segurança:** entradas com aspas, comentários SQL e expressões lógicas devem ser tratadas como valores literais, sem modificar a decisão de autenticação ou provocar erros de sintaxe SQL.
5. **Reavaliar riscos residuais:** armazenamento de senhas, tratamento de erros, permissões e integração com a autenticação do restante da aplicação.

**Esses testes não foram executados nesta análise.** O trecho corrigido acima é uma recomendação, não uma alteração aplicada ou validada em execução.

## 7. Conclusão

**Foi identificada SQL Injection em `agent/app.py:8`, com execução da consulta insegura na linha 10.** A causa é a interpolação direta de `usuario` e `senha` na instrução SQL.

A correção prioritária é utilizar **consulta parametrizada**, passando os valores separadamente a `cursor.execute()`. O risco foi classificado como **alto**, pelo potencial de contornar o login. Não há evidência de exploração ocorrida, e a segurança após a correção dependerá de reanálise e testes.
