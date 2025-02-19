# Use a imagem oficial do MongoDB
FROM mongo:8.0.3

# Defina variáveis de ambiente para configuração
ENV MONGO_REPLICA_PORT 27017
ENV MONGO_REPLICA_HOST localhost
ENV MONGO_INITDB_ROOT_USERNAME root
ENV MONGO_INITDB_ROOT_PASSWORD example

# Comando para iniciar o MongoDB com replicação
CMD mongod --port $MONGO_REPLICA_PORT --replSet rs0 --bind_ip 0.0.0.0 & MONGO0_PID=$!; \
    # Comando para inicializar o conjunto de réplicas
    INIT_REPL_CMD="rs.initiate({ _id: 'rs0', members: [{ _id: 0, host: '$MONGO_REPLICA_HOST:$MONGO_REPLICA_PORT' }] });"; \
    # Comando para criar o usuário root se ele não existir
    INIT_USER_CMD="db.getSiblingDB('admin').getUser('$MONGO_INITDB_ROOT_USERNAME') || db.getSiblingDB('admin').createUser({ user: '$MONGO_INITDB_ROOT_USERNAME', pwd: '$MONGO_INITDB_ROOT_PASSWORD', roles: ['root'] });"; \
    # Aguarde até que o conjunto de réplicas esteja pronto e execute os comandos
    until mongosh --port $MONGO_REPLICA_PORT --eval "$INIT_REPL_CMD" 2>&1 | grep -q "MongoServerError: already initialized"; do sleep 1; done; \
    # Execute o comando para criar o usuário root
    echo "REPLICA SET ONLINE" && mongosh --port $MONGO_REPLICA_PORT --eval "$INIT_USER_CMD"; \
    # Mantenha o contêiner em execução
    wait $MONGO0_PID