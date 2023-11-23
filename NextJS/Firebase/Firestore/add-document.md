# Criação de um Documento no Firestore em um Projeto NextJS

Há duas formas de criar um documento no Firestore, sendo a primeira deixando para o banco de dados gerar o seu ID, e a segunda informando qual ID quer que seja usado para ser o ID do documento.

## Deixar para o banco de dados gerar o ID do documento

Para a criação de um documento no Firestore, comece criando uma função com um bloco `try/catch` (para tratamentos de erros), que será responsável por realizar essa tarefa quando chamada:

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

Nesse caso não criamos uma variável para guardar a referência da coleção, inserindo-a diretamente no `addDoc`.

Não precisa se preocupar em criar a coleção, pois caso ainda não exista será criada, e se existir o registro será adicionado.

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

## Definir o ID do documento

Aconselho que leia antes o tópico anterior caso não tenha lido, visto que o conteúdo desse tópico depende das explicações do anterior: [voltar](#deixar-para-o-banco-de-dados-gerar-o-id-do-documento).

Invéz de obter a referência da coleção, nesse caso será a referência do documento.
Para isso, crie uma variável com o nome no singular da coleção que será adicionado, mais a palavra `Ref`.
Essa variável irá receber a função `doc`, dentro dela o objeto `db`, seguido do nome da coleção mais o `ID` do documento:

```typescript
const userRef = doc(db, "users", "cf5096ed-cec6-4db1-b14a-56f906631c98");
```

Depois no lugar do `addDoc`, cologue `setDoc`:

```typescript
await setDoc(usersCollection, user);
```

Caso prefira, também pode fazer sem o uso da variável de referência do documento:

```typescript
await setDoc(doc(db, "users", "cf5096ed-cec6-4db1-b14a-56f906631c98"), user);
```

Obs.: A função `setDoc` não irá retornar nada.

Código final:

```typescript
const createUser = async () => {
  try {
    const user = {
      name: "Dário",
      age: 18,
      email: "matiasdario75@gmail.com",
    };

    const userRef = doc(db, "users", "cf5096ed-cec6-4db1-b14a-56f906631c98");
    await setDoc(userRef, user);
  } catch (err) {
    console.error(err);
  }
};
```
