# Instalação e Configuração do Prisma ORM

## Instalação

Comece instalando o prisma como depedencência de desenvolvimento:

```bash
npm install prisma --save-dev

ou

yarn add prisma --dev

ou

pnpm add prisma --save-dev

```

## Configuração

Inicie-o:

```bash
npx prisma init
```

### DotEnv

Agora abra o `.gitignore` e insirá o seguinte:

```bash
# DotEnv
*.env
```

Isso fará com que o git ignore os arquivos `.env`, para que não sejam colocados no registro dos commits, matendo as informações contidas nele seguras, como senhas, chaves e demais informações sensíveis.

### Criação da model

Abra o arquivo `schema.prisma` na pasta `prisma`, e crie a sua model:

```prisma
/// prisma/schema.prisma

model Users {
  id       String   @id @default(uuid())
  name     String
  age      Int

  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  @@map("user")
}
```

No terminal envie o seguintee comando para criar a migrate:

```bash
npx prisma migrate dev --name Initial
```

Logo após a migrate será feito a instalação do `@prisma/client`, então não precisa se preocupar com a sua instalação.

### Criação do PrismaService

Com o banco de dados e model finalizados, vamos para a criação do service do prisma.
Novamente no terminal crie um `module` e um `service` chamado prisma da seguinte forma:

```bash
nest g module prisma
```

```bash
nest g service prisma
```

Dentro de `prisma.service` cologue o seguinte:

```typescript
/// src/prisma/prisma.service

import { Injectable, OnModuleInit } from "@nestjs/common";
import { PrismaClient } from "@prisma/client";

@Injectable()
export class PrismaService extends PrismaClient implements OnModuleInit {
  async onModuleInit() {
    await this.$connect();
  }
}
```

E dentro de `prisma.module`:

```typescript
/// src/prisma/prisma.module

import { Global, Module } from "@nestjs/common";

import { PrismaService } from "./prisma.service";

@Global()
@Module({
  providers: [PrismaService],
  exports: [PrismaService],
})
export class PrismaModule {}
```

Com isso conseguirá usar o `PrismaService` globalmente (de qualquer lugar do projeto), dispensando a necessidade de colocar `PrismaService` em todos os `providers` dos `services` que farão o seu uso.

</br>

## Uso do Banco de Dados PostgreSQL

Caso queira usar o PostgreSQL no lugar do SQLite, será necessário rodar o banco de dados de alguma forma para conseguir usá-lo, e uma das solução é usar o Docker.

Obs.: Antes de prosseguir será necessário ter o Docker instalado e configurado, juntamente com a imagem do PostgreSQL.

### DotEnv

No `.env` insera as seguintes informações:

```typescript
DB_NAME = [Nome do banco de dados]
DB_USER = [Nome do usuário]
DB_PASSWORD = [Senha do banco de dados]
DB_HOST = localhost
DB_PORT = [porta do banco de dados]

DATABASE_URL="postgresql://${DB_USER}:${DB_PASSWORD}@${DB_HOST}:${DB_PORT}/${DB_NAME}"
```

Remova os colchetes e as frases que estão dentro, e preencha-os com os dados necessários.

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

</br>

## Referencências

<b>- [Documentação Oficial](https://docs.nestjs.com/recipes/prisma)</b>

#### Vídeos

<b>- [Criando uma aplicação com NestJS e PrismaIO - Daniele Leão](https://youtu.be/0Idug0e9tPw?si=D90zAlxB0VBr4peC)</b>
