# Criação de um Documento no Firestore em um Projeto NextJS

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
