# Exclusão de Documento do Firestore em um Projeto NextJS

Para excluir um documento do Firestore no NextJS, comece criando uma função assíncrona com um bloco `try/catch`:

```typescript
const deleteUser = async () => {
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

Por último use a função `deleteDoc` com a referência do documento que quer excluir:

```typescript
await deleteDoc(userRef);
```

Código final:

```typescript
const deleteUser = async () => {
  try {
    const userRef = doc(db, "users", "cf5096ed-cec6-4db1-b14a-56f906631c98");
    await deleteDoc(userRef);
  } catch (err) {
    console.error(err);
  }
};
```
