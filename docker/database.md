# Criação de Container de Banco de Dados no Docker

Antes de prosseguir será necessário ter o Docker instalado e configurado, juntamente com a imagem do banco de dados.

## Uso do Banco de Dados PostgreSQL

Certifique-se que tenha a imagem do PostgreSQL baixada.

### DotEnv

Em `.env` adicione as seguintes variáveis de ambiente:

```typescript
DB_NAME = [Nome do banco de dados]
DB_USER = [Nome do usuário]
DB_PASSWORD = [Senha do banco de dados]
DB_HOST = localhost
DB_PORT = [porta do banco de dados]
```

Remova os colchetes e as frases que estão dentro, e preencha com os dados necessários.

### Criação do arquivo docker-compose

Próximo passo é criar um container no Docker que irá disponibilizar o banco de dados para ser usado.
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

Obs.: Caso queira parar o container futuramente, não cologue `restart: always` no seu arquivo `docker-compose.yaml`, se não o container sempre será iniciado quando ligar o PC, mesmo que o tenha parado anteriormente.

Não precisa se preocupar em configurar nada no arquivo `docker-compose.yaml`, os dados necessários serão obtidos do seu `.env` e usados.

Por fim, suba o container:

```bash
docker-compose up -d
```

Obs.: Se estiver utilizando Linux e tiver dado erro, cologue `sudo` antes do comando.

### Comandos úteis

#### Parar container

Para parar os containers que estão em execução, primeire use:

```bash
docker ps
```

Esse comando irá listar todos os containers que estão atualmente rodando.
Para parar um container, copie o seu respectivo ID que está na coluna `CONTAINER ID`, depois use o seguinte:

```bash
docker stop [ID DO CONTAINER]
```

#### Rodar Container

Para rodá-lo novamente, use:

```bash
docker start [ID DO CONTAINER]
```

Possa ser que o tenha parado e esquecido o seu ID, nesse caso use:

```bash
docker ps -a
```

Com isso será listado todos os containers criados, incluindo os que não estão rodando.
