# Relatório Técnico — Correção e Validação de SQL Injection

> Relatório gerado pelo **Hermes Agent** utilizando a skill `vulnerability-analysis` durante a etapa de remediação e validação isolada.

## 1. Contexto analisado

Correção aplicada em `agent/app.py`, seguindo a skill `vulnerability-analysis` e autorização explícita do estudante.

Antes da alteração, foi criado **`agent/app_vulneravel.py`**, confirmado como cópia idêntica, byte a byte, do arquivo original. Nenhum outro arquivo foi alterado ou criado.

## 2. Vulnerabilidade identificada

A consulta da linha 8 utilizava uma f-string para inserir `usuario` e `senha` diretamente no SQL. Isso permitia que entradas manipuladas alterassem a condição de autenticação.

## 3. Evidências técnicas

Antes da correção, um teste isolado com SQLite em memória reproduziu o problema: uma tentativa de injeção retornou **`Acesso Permitido!`**, quando deveria retornar **`Acesso Negado.`**

Após a correção, o mesmo cenário foi negado.

## 4. Impacto, probabilidade e severidade

O achado original foi classificado como **alto**, pelo potencial de contornar a verificação de credenciais quando entradas não confiáveis chegam à função.

A causa identificada foi removida desta consulta. Isso **não equivale a declarar toda a aplicação segura**.

## 5. Correção aplicada

Código corrigido de `agent/app.py`:

```python
import sqlite3

def fazer_login(usuario, senha):
    conexao = sqlite3.connect('banco_de_dados.db')
    cursor = conexao.cursor()

    query = "SELECT * FROM usuarios WHERE username = ? AND password = ?"

    cursor.execute(query, (usuario, senha))
    resultado = cursor.fetchone()
    conexao.close()

    if resultado:
        return "Acesso Permitido!"
    else:
        return "Acesso Negado."
```

**Somente duas linhas lógicas foram alteradas:**
- A consulta agora utiliza os marcadores `?`.
- `cursor.execute()` recebe os valores separadamente, pela tupla `(usuario, senha)`.

Assim, os valores são tratados como dados, não como parte da estrutura SQL. Foram preservados a assinatura da função, o banco utilizado, a consulta à tabela, o fechamento da conexão e as mensagens de retorno.

## 6. Validação

**Os 9 testes executados passaram**, utilizando SQLite real em memória, sem acessar o banco do projeto:

1. Credenciais válidas.
2. Senha incorreta.
3. Usuário inexistente.
4. Entradas vazias.
5. Tentativa de injeção no usuário.
6. Tentativa de injeção na senha.
7. Credenciais legítimas contendo aspas.
8. Texto semelhante a SQL como usuário literal válido.
9. Texto semelhante a SQL como senha literal válida.

Também foi verificado o fechamento da conexão em cada cenário e confirmado que o conteúdo difere do backup apenas nas duas linhas previstas.

**Ainda precisam ser realizados:**
- Teste de integração com uma cópia controlada do banco e seu esquema real.
- Teste do fluxo completo que chama `fazer_login`, incluindo a concessão efetiva de acesso.
- Testes de falhas do banco, como arquivo indisponível ou tabela ausente.
- Revisão do armazenamento de senhas e dos demais caminhos de autenticação, fora desta correção pontual.

## 7. Conclusão

**A consulta foi parametrizada e os testes isolados confirmaram o bloqueio dos cenários de SQL Injection testados.** O comportamento funcional esperado foi preservado.

O backup `agent/app_vulneravel.py` mantém a falha intencionalmente para fins educacionais: não deve ser utilizado como implementação de login.
