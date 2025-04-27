# Curso de Redis

Este documento compila os principais tópicos estudados no curso de Redis, organizados de forma didática e prática.

## Índice

1. [Instalação do Redis via Docker](#instalacao-docker)
2. [Trabalhando com Redis no Python](#python-redis)
3. [TTL (Time to Live) no Redis](#ttl)
4. [Pub/Sub no Redis](#pubsub)

---

## 1. <a name="instalacao-docker"></a> Instalação do Redis via Docker

### Comandos Básicos:

```bash
# Rodar container Redis
docker run -d --name redis -p 6380:6379 redis

# Verificar containers em execução
docker ps

# Acessar o Redis do host externo
rdcli -u redis://default:senha@url-do-redis:porta
rdcli -h localhost -p 6380
ping
exit

# Acessar bash do container
docker exec -it redis /bin/bash
redis-cli ping
redis-cli INFO
redis-cli
exit
exit

# Parar o container
docker stop redis

# Iniciar o container
docker start redis

# Reiniciar o container
docker restart redis
```

### Observação:
- É possível utilizar ferramentas como **Redis Insight** para acessar o Redis de forma visual.

---

## 2. <a name="python-redis"></a> Trabalhando com Redis no Python

### Instalação
```bash
pip install redis
```

### Exemplos de Manipulação

```python
import redis

# Conectar ao Redis
r = redis.Redis(host='localhost', port=6380, db=0)

# Hash
r.hset('user:1000', 'name', 'Alice')
r.hset('user:1000', 'age', 30)
print(r.hgetall('user:1000'))
r.hset('user:1000', 'age', 31)
print(r.hget('user:1000', 'age'))

# List
r.rpush('tasks', 'task1', 'task2', 'task3')
print(r.lrange('tasks', 0, -1))
print(r.lpop('tasks'))
print(r.llen('tasks'))

# Set
r.sadd('tags', 'python', 'redis', 'database')
print(r.smembers('tags'))
print(r.sismember('tags', 'python'))
r.srem('tags', 'database')

# Sorted Set
r.zadd('leaderboard', {'Alice': 100, 'Bob': 200, 'Charlie': 150})
print(r.zrange('leaderboard', 0, -1, withscores=True))
r.zincrby('leaderboard', 50, 'Alice')
print(r.zscore('leaderboard', 'Alice'))
print(r.zrangebyscore('leaderboard', 100, 200, withscores=True))
```

---

## 3. <a name="ttl"></a> TTL (Time to Live) no Redis

### Conceito
- TTL define um tempo de vida para as chaves.
- Após o tempo estipulado, a chave é removida automaticamente.

### Exemplos Práticos

#### Cache de Respostas de API
```bash
SET api_response:users "{'users': ['Bruce', 'Clark']}"
EXPIRE api_response:users 60
TTL api_response:users
```

#### Gerenciamento de Sessões
```bash
SET session:user1234 "session_data"
EXPIRE session:user1234 1800
TTL session:user1234
```

#### Limite de Taxa (Rate Limiting)
```bash
SET requests:user1234 1
INCR requests:user1234
EXPIRE requests:user1234 60
TTL requests:user1234
```

#### Códigos Temporários (OTP)
```bash
SET verification_code:user1234 "123456"
EXPIRE verification_code:user1234 300
TTL verification_code:user1234
```

#### Paginação de Resultados
```bash
SET search_results:page1 "{'results': ['search1', 'search2']}"
EXPIRE search_results:page1 600
TTL search_results:page1
```

### Comandos Relacionados
- `EXPIRE key seconds`: Define o TTL de uma chave.
- `TTL key`: Verifica o tempo restante.
- `PERSIST key`: Remove o TTL de uma chave.
- Parâmetros avançados de EXPIRE: `NX`, `XX`, `GT`, `LT`.

---

## 4. <a name="pubsub"></a> Pub/Sub no Redis

### Comandos Principais:
- `PUBLISH`: Publica uma mensagem.
- `SUBSCRIBE`: Inscreve-se em canais.
- `UNSUBSCRIBE`: Cancela inscrição.
- `PSUBSCRIBE`: Inscreve em padrões de canais.
- `PUNSUBSCRIBE`: Cancela inscrição em padrões.
- `PUBSUB`: Exibe informações do sistema Pub/Sub.

### Exemplos:

#### Sistema de Notificações em Tempo Real
```bash
# Subscriber
redis-cli
SUBSCRIBE task_updates

# Publisher
redis-cli
PUBLISH task_updates "Tarefa 123 foi atualizada"
PUBLISH task_updates "Tarefa 456 foi atualizada"
```

#### Sistema de Chat em Tempo Real
```bash
# Subscriber 1 e 2
redis-cli
SUBSCRIBE chat_room

# Publisher
redis-cli
PUBLISH chat_room "Olá a todos!"
PUBLISH chat_room "Bye Bye"
```

#### Sistema de Alertas Críticos
```bash
# Subscriber
redis-cli
SUBSCRIBE critical_alerts

# Publisher
redis-cli
PUBLISH critical_alerts "Servidor inativo"
PUBLISH critical_alerts "Erro geral"
```

#### Gerenciamento e Monitoramento
```bash
redis-cli
PUBSUB channels
PUBSUB numsub task_updates
PUBSUB numsub chat_room
PUBSUB numsub critical_alerts
```

---

**Fim do documento.**

Organizado para referência rápida no GitHub!

