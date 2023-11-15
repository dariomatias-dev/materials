# Instalação e configuração do Fastify no NestJS

Ao criar o projeto, instale a seguintes dependências:

```bash
npm install --save @nestjs/platform-fastify fastify
ou
yarn add @nestjs/platform-fastify fastify
ou
pnpm add @nestjs/platform-fastify fastify
```

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
