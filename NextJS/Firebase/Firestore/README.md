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

Na aba `regras`, abaixo do título `Cloud Firestore`, mude a regra da linha ` allow read, write: if false` de `false` para `true` e publique:

<img src="images/edit-rules.png" alt="Copy settings" />

Obs.: Essa configuração é apenas para podermos interagir com o Firestore, mas para projetos reais crie regras seguras que atendam as necessidades do projeto.

### DotEnv

Abra o arquivo `.env` do seu projeto NextJS, cole o que copiou anteriormente da variável `firebaseConfig` e cologue as seguintes variáveis de ambiente:

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

### Configuração do Firebase no NextJS

Dentro da pasta `src` crie uma nova pasta chamada `services` e dentro dela uma pasta com o nome `firebase`, com um arquivo `index.ts`.
Com o arquivo aberto cole o seguinte:

```typescript
/// src/services/firebase/index.ts

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

export {
  addDoc,
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

### Utilização do Firestore no projeto NextJS

Obs.:

- Para poder exemplificar irei utilizar um registro de usuário, com nome, idade e email, Adapte conforme as suas necessidades.
- Todos os recursos usados do `Firestore` serão importados do arquivo `src/services/firebase/index.ts`.

#### Criação de um registro

Para a criação de um registro no Firestore, comece criando uma função com um bloco `try/catch` (para tratamentos de erros), que será responsável por realizar essa tarefa quando chamada:

```typescript
const createUser = async () => {
  try {
  } catch (err) {
    console.error(err);

    /* Crie o seu tratamento de erros aqui */
  }
};
```

Ela terá que ser assíncrona para esperar a requisição de adição de registro ser finalizada, e caso dê algum erro durate o processo, fazer o devido tratamento no bloco `catch`.

Próximo passo será pegar uma referência da coleção onde esse registro será adicionado.
Para isso crie uma variável com o nome da coleção mais o nome `Collection`.
Essa função receberá a função `collection` e dentro dela o objeto `db` vírgula o nome da coleção:

```typescript
const usersCollection = collection(db, "users");
```

Com o objeto do registro que quer adicionar em mãos, adicione um `await` seguido da função `addDoc`.
Dentro de `addDoc` cologue a referência da coleção, vírgula o objeto que quer adicionar:

```typescript
const user = {
  name: "Dário",
  age: 18,
  email: "matiasdario75@gmail.com",
};

const usersCollection = collection(db, "users");
await addDoc(usersCollection, user);
```

Obs.: Também pode ser feito dessa maneira se preferir:

```typescript
const user = {
  name: "Dário",
  age: 18,
  email: "matiasdario75@gmail.com",
};

await addDoc(collection(db, "users"), user);
```

Não precisa se preocupar em criar a coleção, pois caso não exista ainda será criada, e se existir, o registro será adicionado.

Caso queira saber qual é o ID do registro, crie uma variável que irá receber a referência do documento e depois acesse a propriedade `id`:

```typescript
const docRef = await addDoc(usersCollection, user);
console.log(docRef.id);
```

Código final:

```typescript
const createUser = async () => {
  try {
    const user = {
      name: "Dário",
      age: 18,
      email: "matiasdario75@gmail.com",
    };

    const usersCollection = collection(db, "users");
    const docRef = await addDoc(usersCollection, user);
    console.log(docRef.id);
  } catch (err) {
    console.error(err);
  }
};
```
