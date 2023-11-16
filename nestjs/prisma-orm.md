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

Para criar o container com o PostgreSQL, leia [Criação de Container de Banco de Dados no Docker](../docker//database.md) na seção <strong>Uso do Banco de Dados PostgreSQL</strong>.

### Prisma

Abra o arquivo `schema.prisma` dentro da pasta `prisma` que está na raiz do projeto, depois mude o `provider` de `sqlite` para `postgresql` caso esteja:

Antes:

```prisma
/// prisma/schema.prisma

datasource db {
  provider = "sqlite"
  url      = env("DATABASE_URL")
}
```

Depois:

```prisma
/// prisma/schema.prisma

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}
```

Com isso o Prisma passará a usar como banco de dados o PostgreSQL no lugar do SQLite.

### DotEnv

No arquivo `.env` cologue a seguinte URL do banco de dados que o Prisma fará uso:

```bash
DATABASE_URL="postgresql://${DB_USER}:${DB_PASSWORD}@${DB_HOST}:${DB_PORT}/${DB_NAME}"
```

As informações `DB_USER, DB_PASSWORD, DB_HOST e DB_NAME` serão obtidas das variáveis de ambiente que inseriu anteriormente na criação do container do PostgreSQL, então não será preciso mudar nada.

### Lembrete

Lembre-se de rodar a migrate novamente para as tabelas definidas no seu `schema.prisma` serem criadas no banco de dados do container, e exclua o arquivo do banco de dados do `SQLite` caso tenha sido criado, visto que não será usado.

### Finalização

Para usar o `PrismaService`, cologue-o no contrutor do `service` que irá usá-lo:

```typescript
constructor(
  private readonly prisma: PrismaService,
) {}
```

</br>

## Referencências

<b>- [Documentação Oficial](https://docs.nestjs.com/recipes/prisma)</b>

#### Vídeos

<b>- [Criando uma aplicação com NestJS e PrismaIO - Daniele Leão](https://youtu.be/0Idug0e9tPw?si=D90zAlxB0VBr4peC)</b>
