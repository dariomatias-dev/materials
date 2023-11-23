# Atuaização de Documento do Firestore em um Projeto NextJS

Para atualizar um documento do Firestore no NextJS, comece criando uma função assíncrona com um bloco `try/catch`:

```typescript
const updateUser = async () => {
  try {
  } catch (err) {
    console.error(err);

    /* Crie o seu tratamento de erros aqui */
  }
};
```

Próximo passo será obter uma referência do documento.
Para isso crie uma variável com o nome da coleção no singular, juntamente com a palavra `Ref`.
Essa variável irá receber a função `doc`, dentro dela o objeto `db`, o nome da coleção e o ID do documento que quer obter:

```typescript
const userRef = doc(db, "users", "cf5096ed-cec6-4db1-b14a-56f906631c98");
```

Por último use a função `updateDoc` com a referência do documento e o objeto com o(s) novo(s) dado(s):

```typescript
const user = {
  age: 19,
};

await updateDoc(userRef, user);
```

Código final:

```typescript
const updateUser = async () => {
  try {
    const user = {
      age: 19,
    };

    const userRef = doc(db, "users", "cf5096ed-cec6-4db1-b14a-56f906631c98");
    await updateDoc(userRef, user);
  } catch (err) {
    console.error(err);
  }
};
```
