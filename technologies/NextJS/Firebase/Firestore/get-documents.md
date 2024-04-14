# Obtenção de Documentos do Firestore em um Projeto NextJS

Para a obtenção de documentos no Firestore, comece criando uma função assíncrona com um bloco `try/catch` (para tratamento de erros), que será responsável por realizar essa tarefa quando chamada:

```typescript
const getUsers = async () => {
  try {
  } catch (err) {
    console.error(err);

    /* Crie o seu tratamento de erros aqui */
  }
};
```

Próximo passo será obter uma referência da coleção onde esse registro será adicionado.
Para isso, crie uma variável com o nome da coleção juntamente com o nome `Collection`.
Essa variável irá receber a função `collection`, e dentro dela o objeto `db` seguido do nome da coleção:

```typescript
const usersCollection = collection(db, "users");
```

Crie uma variável com o nome da coleção mais `Snapshot`.
Cologue `await` seguindo da função `getDocs`, e dentro dela a referência da coleção:

```typescript
const usersSnapshot = await getDocs(usersCollection);
```

Agora só precisamos obter os documentos.
Crie uma nova variável com o nome da coleção, que irá receber `usersSnapshot`, acessando a propriedade `docs` mais o método de array `map` para interar sobre os documentos.
Dos documentos, apenas queremos os seus dados, então acesse a propriedade `data()` e retorne o resultado:

```typescript
const users = usersSnapshot.docs.map((doc) => {
  return doc.data();
});
```

Com isso a variável `users` terá todos os dados dos documentos da coleção.

Código final:

```typescript
const getUsers = async () => {
  const usersCollection = collection(db, "users");
  const usersSnapshot = await getDocs(usersCollection);
  const users = usersSnapshot.docs.map((doc) => {
    return doc.data();
  });
};
```

</br>
</br>
</br>

Caso queira o ID do documento, e atribuí-lo ao objeto que será retornado, pode fazer da seguinte maneira:

```typescript
const users = usersSnapshot.docs.map((doc) => {
  return {
    id: doc.id,
    ...doc.data(),
  };
});
```

A propriedade `ID` do objeto `doc`, contém o ID do documento.

Código final:

```typescript
const getUsers = async () => {
  const usersCollection = collection(db, "users");
  const usersSnapshot = await getDocs(usersCollection);
  const users = usersSnapshot.docs.map((doc) => {
    return {
      id: doc.id,
      ...doc.data(),
    };
  });
};
```
