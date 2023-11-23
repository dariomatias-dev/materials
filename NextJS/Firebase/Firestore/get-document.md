# Obtenção de um Documento do Firestore em um Projeto NextJS

Para obter um documento do Firestore no NextJS, comece criando uma função assíncrona com um bloco `try/catch`:

```typescript
const getUser = async () => {
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

Depois crie uma nova variável com o nome da coleção no singular mais a palavra `Doc`, recebendo a função `getDoc` com a referência do documento:

```typescript
const userDoc = await getDoc(userRef);
```

Último passo é verificar se o documento existe.
A variável que acabou de criar possue um método `exists()`, que ao acessar retornará um booleano indicando se o documento existe ou não.
Sendo assim, só precisamos criar um `if` acessando essa propriedade para fazer a verificação:

```typescript
if (userDoc.exists()) {
  console.log(userDoc.data());
} else {
  console.log("Registro não existe.");
}
```

Código final:

```typescript
const getUser = async () => {
  const userRef = doc(db, "users", "cf5096ed-cec6-4db1-b14a-56f906631c98");
  const userDoc = await getDoc(userRef);

  if (userDoc.exists()) {
    console.log(userDoc.data());
  } else {
    console.log("Registro não existe.");
  }
};
```
