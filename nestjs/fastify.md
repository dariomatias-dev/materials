# Instalação e configuração do Fastify no NestJS

## Instalação

Ao criar o projeto, instale a seguintes dependências:

```bash
npm install --save @nestjs/platform-fastify fastify

ou

yarn add @nestjs/platform-fastify fastify

ou

pnpm add @nestjs/platform-fastify fastify
```

## Configuração

Abra o `main.ts` e insirá o seguinte:

```bash
/// main.ts

import { NestFactory } from '@nestjs/core';
import {
  FastifyAdapter,
  NestFastifyApplication,
} from '@nestjs/platform-fastify';

import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create<NestFastifyApplication>(
    AppModule,
    new FastifyAdapter({
      logger: true,
    }),
  );

  await app.listen(3000);
}
bootstrap();
```

Embora não tenha sido utilizado a dependência `fastify`, ela será usada quando for necessário tipar algo que irá ser um `Response` ou `Request`, como `FastifyReply` ou `FastifyRequest` respectivamente.

Obs.:

1. Para facilitar a depuração e monitoramento durante o desenvolvimento, habilitamos o registro de log através do `logger: true`.

## Habilitar cors

#### Permitir que qualquer origem acesse a API

Há duas maneiras distintas de permitir o acesso à API por clientes de qualquer domínio, mas ambas resultando no mesmo efeito:

```bash
{
  cors: true,
}
```

```bash
  const app = await NestFactory.create<NestFastifyApplication>(
    AppModule,
    new FastifyAdapter({
      logger: true,
    }),
    {
      cors: true,
    },
  );
```

<hr></hr>

```bash
app.enableCors();
```

```bash
async function bootstrap() {
  const app = await NestFactory.create<NestFastifyApplication>(
    AppModule,
    new FastifyAdapter({
      logger: true,
    }),
  );

  app.enableCors();

  await app.listen(3000);
)
```

#### Permitir que origens específicas possam acessar a API e seu métodos

Se quiser que apenas determinado dominío consiga acessar a API, abra uma chave dentro de `enableCors` e na propriedade `origin` cologue a URL do domínio:

```bash
app.enableCors({
  origin: 'http://localhost:3000',
});
```

É possível também definir quais métodos o dominío irá conseguir acessar, para isso utilize a propriedade `methods` de `enableCors` com os respectivos métodos:

```bash
methods: 'GET, POST, PUT, PATCH, DELETE',

ou

methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE'],
```

```bash
app.enableCors({
  origin: 'http://localhost:3000',
  methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE'],
});
```

Use a forma de escrita que achar melhor para o seu projeto.

Obs.: Os métodos usados são apenas para exemplificação, podendo ser qualquer um (`GET, POST, PUT, PATCH, DELETE, OPTIONS, HEAD, TRACE e CONNECT`).

##### Compartilhar configurações

Se tiver um dominío que terá as mesmas configurações de um existente, pode adicioná-lo no mesmo `enableCors`, da seguinte maneira:

```bash
app.enableCors({
  origin: [
    'http://localhost:3000',
    'http://localhost:3001',
  ],
});
```

Dessa forma irão compartilhar as mesmas configurações.

##### Dominíos com configurações diferentes

No entanto, se os domínios tiverem configurações diferentes, será necessário fazer a sua configuração separadamente:

```bash
app.enableCors({
  origin: 'http://localhost:3000',
  methods: ['GET'],
});

app.enableCors({
  origin: 'http://localhost:3001',
  methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE'],
});
```

## Remoção de dependências desnecessárias

Com a finalização da instalação e configuração do Fastify em seu projeto NestJS, poderá fazer a remoção do Express e das depedências relacionadas da seguinte maneira:

```bash
npm remove @nestjs/platform-express @types/express

ou

yarn remove @nestjs/platform-express @types/express

ou

pnpm remove @nestjs/platform-express @types/express
```