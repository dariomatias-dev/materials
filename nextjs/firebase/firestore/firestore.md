# Firestore no NextJS

## Instalação

No seu projeto NextJS, instale o `firebase`:

```bash
npm install firebase

ou

yarn add firebase

ou

pnpm add firebase
```

## Configuração

### Firebase

No console do projeto, clique na engrenagem que está na parte superior esquerda, logo depois de `Visão geral do projeto`, em seguida na opção `Configurações do projeto`:

<img src="images/project-settings-option.png" alt="Project settings option" />

Role para baixo e na seção `Seus aplicativos` clique no ícone `</>`:

<img src="images/project-settings-screen.png" alt="Project settings screen" />

Cologue o apelido do aplicativo em `Apelido do app` e aceite `Configure também o Firebase Hosting para este app.` caso queira.

<img src="images/app-register-screen.png" alt="App register screen" />

No passo 2, copie o conteúdo da variável `firebaseConfig`:

<img src="images/copy-settings.png" alt="Copy settings" />

Aperte em próximo, depois próximo e `Continuar no console`.

### NextJS

#### DotEnv

Abra o arquivo `.env`, cole o que copiou anteriormente da variável `firebaseConfig` e cologue as seguintes variáveis de ambiente:

```bash
NEXT_PUBLIC_API_KEY =
NEXT_PUBLIC_AUTH_DOMAIN =
NEXT_PUBLIC_PROJECT_ID =
NEXT_PUBLIC_STORAGE_BUCKET =
NEXT_PUBLIC_MESSAGING_SENDER_ID =
NEXT_PUBLIC_APP_ID =
NEXT_PUBLIC_MEASUREMENT_ID =
```

Agora passe cada dado para as suas respectivas variáveis de ambiente.

#### Configuração do Firebase no NextJS

Dentro da pasta `src` crie uma nova pasta chamada `services` e dentro dela uma pasta com o nome `firebase`, com um arquivo `index.ts`.
Com o arquivo aberto cole o seguinte:

```typescript
/// src/services/firebase/index.ts

import { getAnalytics } from "firebase/analytics";
import { initializeApp } from "firebase/app";
import {
  addDoc,
  collection,
  deleteDoc,
  doc,
  getDoc,
  getDocs,
  getFirestore,
  query,
  setDoc,
  updateDoc,
  where,
} from "firebase/firestore";

const firebaseConfig = {
  apiKey: process.env.NEXT_PUBLIC_API_KEY,
  authDomain: process.env.NEXT_PUBLIC_AUTH_DOMAIN,
  projectId: process.env.NEXT_PUBLIC_PROJECT_ID,
  storageBucket: process.env.NEXT_PUBLIC_STORAGE_BUCKET,
  messagingSenderId: process.env.NEXT_PUBLIC_MESSAGING_SENDER_ID,
  appId: process.env.NEXT_PUBLIC_APP_ID,
  measurementId: process.env.NEXT_PUBLIC_MEASUREMENT_ID,
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);
const analytics = getAnalytics(app);

export {
  addDoc,
  analytics,
  collection,
  db,
  deleteDoc,
  doc,
  getDoc,
  getDocs,
  query,
  setDoc,
  updateDoc,
  where,
};
```

Obs.: Esse é um exemplo genérico do código de inicialização do firebase, fique a vontade para adicionar ou remover elementos conforme as necessidades do seu projeto.

Ao exportar a instância do banco de dados juntamente com os métodos do `firestore`, podemos ter apenas uma importação nos arquivos que irão usar o firebase, em vez de duas ou mais.
