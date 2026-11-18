# Fundamentos de Segurança da Informação

## 1. Conceitos fundamentais

### Vulnerabilidade
Fraqueza em um sistema, aplicação ou configuração que pode ser explorada.

### Ameaça
Evento ou agente capaz de explorar uma vulnerabilidade e causar danos.

### Risco
Combinação da probabilidade de exploração de uma vulnerabilidade com seu impacto potencial.

## 2. Tríade CIA

### Confidencialidade
Garantir que informações sejam acessadas somente por pessoas autorizadas.

### Integridade
Garantir que informações não sejam alteradas indevidamente.

### Disponibilidade
Garantir que sistemas e informações permaneçam acessíveis quando necessários.

## 3. SQL Injection

SQL Injection é uma vulnerabilidade que ocorre quando entradas não confiáveis são incorporadas à estrutura de comandos SQL.

### Causas comuns

- Concatenação de entradas do usuário em consultas SQL.
- Uso de f-strings para construir comandos SQL.
- Ausência de consultas parametrizadas.

### Impactos possíveis

- Acesso não autorizado.
- Exposição de informações confidenciais.
- Alteração ou exclusão de dados.
- Comprometimento da autenticação.

### Prevenção

- Utilizar consultas parametrizadas.
- Aplicar o princípio do menor privilégio.
- Armazenar senhas com algoritmos apropriados para senhas, como Argon2id.
- Evitar exposição de mensagens internas do banco de dados.

## 4. Análise de riscos

Para cada vulnerabilidade, considerar:

- Evidência técnica.
- Facilidade de exploração.
- Exposição do sistema.
- Impacto sobre confidencialidade, integridade e disponibilidade.
- Controles de segurança existentes.

Classificar a severidade como baixa, média, alta ou crítica, justificando a avaliação.

## 5. Boas práticas defensivas

- Manter sistemas e dependências atualizados.
- Aplicar o princípio do menor privilégio.
- Monitorar eventos de segurança.
- Realizar backups.
- Validar correções por meio de novos testes.
- Executar testes somente em ambientes autorizados.
