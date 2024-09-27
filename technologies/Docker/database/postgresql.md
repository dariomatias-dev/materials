# Criação de Container Docker para Banco de Dados PostgreSQL

Antes de prosseguir será necessário ter o Docker instalado e configurado, juntamente com a imagem do PostgreSQL.

## Uso do Banco de Dados PostgreSQL

### DotEnv

Crie um arquivo `.env` na raiz do projeto, e adicione as seguintes variáveis de ambiente:

```bash
DB_NAME=[Nome do banco de dados]
DB_USER=[Nome do usuário]
DB_PASSWORD=[Senha do banco de dados]
DB_HOST=localhost
DB_PORT=[porta do banco de dados]
```

Remova os colchetes e frases, e preencha com os respectivos dados solicitados.

### Criação do Arquivo docker-compose

Comece criando um arquivo `docker-compose.yaml` na raiz do projeto, e cologue o seguinte:

```yaml
version: "3.8"

services:
  postgres:
    image: postgres:latest
    container_name: ${DB_NAME}
    restart: always
    environment:
      POSTGRES_USER: ${DB_USER}
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    volumes:
      - pg-data:/var/lib/postgresql/data
    ports:
      - "${DB_PORT}:5432"

volumes:
  pg-data:
```

**Obs.:** Se desejar parar o container no futuro, não adicione `restart: always` no arquivo `docker-compose.yaml`. Caso contrário, o container será reiniciado automaticamente toda vez que o sistema for iniciado, mesmo que tenha sido previamente interrompido.

Não é necessário configurações adicionais no arquivo `docker-compose.yaml`, pois os dados necessários serão obtidos do `.env`.

Agora suba o container:

```bash
docker-compose up -d
```

Obs.: Se estiver utilizando Linux e tiver ocorrido erro, cologue `sudo` antes do comando.

### Criação do Banco de Dados no Container

Para criar um banco de dados dentro do container, siga os passos abaixo:

1 - Use o seguinte comando para entrar no shell do container:

```bash
docker exec -it [nome do container] bash
```

2 - No shell do container, use o SQL abaixo para criar o banco de dados:

```bash
CREATE DATABASE "nome do banco de dados";
```

3- Para se conectar ao banco de dados que acabou de criar, use o comando:

```bash
psql -U [nome do usuário] -d [nome do banco de dados]
```

### Comandos úteis

#### Listar Containers em Execução:

Use o seguinte comando para listar todos os containers que estão em execução:

```bash
docker ps
```

#### Listar Todos os Containers

Para listar todos os containers criados (incluindo os que não estão em execução), utilize o seguinte comando:

```bash
docker ps -a
```

#### Parar container

Copie o respectivo ID do container que está na coluna `CONTAINER ID`, depois use:

```bash
docker stop [ID DO CONTAINER]
```

#### Rodar Container

Para rodar um container que foi parado, use:

```bash
docker start [ID DO CONTAINER]
```
