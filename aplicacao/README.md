# Versão do NodeJS

- Nesse projeto estamos usando a versão **16.0.0** do NodeJS

# Iniciado o projeto

Antes de subir o projeto em kubernetes, subi a aplicação em docker mesmo para garantir que não tenha nenhum erro nos containers

```bash
docker-compose up # para baixar as imagens localmente e iniciar os containers

# aguarde as log de criação do banco de dados e a mensagem abaixo informando que a aplicação está no ar
# Mensagem de sucesso do banco de dados:
db_1   | 2024-11-15T21:07:07.432234Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060, socket: /var/run/mysqld/mysqlx.sock
db_1   | 2024-11-15T21:07:07.432373Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '9.1.0'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.

# mensagem de sucesso da aplicação:
app_1  | 
app_1  | 9:07:07 PM - Found 0 errors. Watching for file changes.
app_1  | server running on port 3000
app_1  | App Data Source inicializado
```

# Deploy da aplicação em kubernetes
- O deploy em kubernetes está dentro da pasta aplicacao em uma pasta chamada k8s 


