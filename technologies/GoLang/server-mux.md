# Construindo um Servidor HTTP com ServeMux: O Roteador Padrão do Go

O **ServeMux** é um roteador HTTP básico do Go, utilizado para associar URLs a funções de tratamento (handlers). Ele é parte do pacote padrão **net/http** e oferece uma maneira simples e eficiente de criar servidores HTTP sem dependências externas. Neste guia, vamos aprender a configurar e usar o **ServeMux** para criar um servidor HTTP básico e gerenciar rotas, parâmetros e requisições.

**Versão do Go utilizada: 1.23.4**

## 1. Criar o Servidor

Primeiramente, crie uma instância de **ServeMux** utilizando a função **NewServeMux()** do pacote **http**. Em seguida, use **http.ListenAndServe** para iniciar o servidor:

```go
package main

import (
	"fmt"
	"log"
	"net/http"
)

func main() {
	// Criação da instância ServeMux
	api := http.NewServeMux()

	fmt.Println("Server listening on http://localhost:3030")
	// Configuração do servidor para escutar na porta 3030
	if err := http.ListenAndServe(":3030", api); err != nil {
		log.Fatal(err)
	}
}
```

### Como Executar:

No terminal, execute o comando:

```sh
go run [caminho do arquivo]
```

Isso fará o servidor começar a escutar na URL **http://localhost:3030**.

## 2. Criar Rotas

Para criar uma rota, use o método **HandleFunc** da instância **ServeMux** que criou. O primeiro argumento é o método HTTP (**POST**, **GET**, **PATCH**, **PUT**, **DELETE**) e o caminho, e o segundo é a função de tratamento que será executada quando a requisição for feita:

```go
api.HandleFunc("GET /user", func(w http.ResponseWriter, r *http.Request) {})
```

### Funções de Retorno de Resposta

Dentro dessa função, pode usar diferentes métodos para retornar respostas ao cliente, como **w.Write**, **w.WriteHeader** e **http.Error**:

1. **`w.Write`**: Envia o corpo da resposta. Pode usá-lo para retornar uma mensagem simples ou dados em formato JSON, por exemplo: **w.Write([]byte("User route"))** envia a mensagem **"User route"** como resposta.

2. **`w.WriteHeader`**: Usada para definir o código de status HTTP da resposta. Se não for chamada explicitamente, o código de status será 200 (OK) por padrão. Exemplo:

```go
api.HandleFunc("GET /forbidden", func(w http.ResponseWriter, r *http.Request) {
	w.WriteHeader(http.StatusForbidden)
	w.Write([]byte("Acesso proibido"))
})
```

Nesse caso, o código de status 403 (Proibido) será enviado junto com a mensagem **"Acesso proibido"**.

1. **`http.Error`**: Utilizado para enviar uma resposta de erro com um código de status e uma mensagem explicativa. Essa função é útil para indicar problemas, como quando há uma falha no servidor. Exemplo:

```go
api.HandleFunc("GET /user", func(w http.ResponseWriter, r *http.Request) {
	http.Error(w, "Usuário não encontrado", http.StatusNotFound)
})
```

Isso retorna uma resposta com código 404 (não encontrado) e a mensagem **"Usuário não encontrado"**.

### Como Testar a Rota:

Abra um terminal e use o comando **curl**:

```sh
curl http://localhost:3030/user
```

### Capturar Todas as Requisições que Começam com Determinado Prefixo

Para criar uma rota que capture todas as requisições que começam com determinado prefixo, como **"/user"**, basta usar o caminho **"/user/"**:

```go
api.HandleFunc("GET /user/", func(w http.ResponseWriter, r *http.Request) {
	// Captura a parte da URL após "/user/"
	path := r.URL.Path[len("/user/"):]

	// Responde com o caminho capturado
	w.Write([]byte(fmt.Sprintf("Requisição para /user/%s", path)))
})
```

### Como Funciona:

1. **Rota "/user/"**: Captura todas as requisições que começam com **"/user"**.
2. **Captura do Caminho**: Dentro da função de tratamento, o código **r.URL.Path[len("/user/"):]** obtém a parte da URL após **"/user/"**.
3. **Resposta**: A resposta mostra a parte da URL após o prefixo.

### Como Testar:

- Para **/user/123**:

```sh
curl http://localhost:3030/user/123
```

Resposta: **Requisição para /user/123**

- Para **/user/john**:

```sh
curl http://localhost:3030/user/john
```

Resposta: **Requisição para /user/john**

- Para **/user/john/123**:

```sh
curl http://localhost:3030/user/john/123
```

Resposta: **Requisição para /user/john/123**

## 3. Acessar Parâmetros na URL

Para capturar parâmetros dinâmicos de uma URL, defina a sua posição e nome. Para acessar os valores desses parâmetros, utilize o método **r.PathValue**:

```go
api.HandleFunc("GET /user/{id}", func(w http.ResponseWriter, r *http.Request) {
	userId := r.PathValue("id")  // Captura o valor do parâmetro {id} da URL

	w.Write([]byte(userId))
})
```

### Como Testar:

Acesse a URL com o parâmetro:

```sh
curl http://localhost:3030/user/123
```

A resposta será **"123"**, que é o valor do parâmetro **{id}**.

## 4. Acessar Query Parameters na URL

Para acessar parâmetros de consulta (query parameters) da URL, use o método **r.URL.Query().Get("nome_do_parametro")**:

```go
api.HandleFunc("GET /user", func(w http.ResponseWriter, r *http.Request) {
	code := r.URL.Query().Get("code")  // Obtém o valor do query parameter "code"

	w.Write([]byte(code))
})
```

### Como Testar:

Acesse a URL com o parâmetro de consulta **code**:

```sh
curl "http://localhost:3030/user/123?code=abc"
```

A resposta será **"abc"**, que é o valor do parâmetro **code**.

Obs.: Caso o parâmetro de consulta esteja ausente, o valor obtido será uma string vazia.

## 5. Acessar o Corpo da Requisição

Para acessar o corpo da requisição, use o pacote **io** para ler o conteúdo enviado. Exemplo:

```go
body, err := io.ReadAll(r.Body)
if err != nil {
	log.Fatal(err)
}
```

### Exemplo Completo:

```go
api.HandleFunc("POST /user", func(w http.ResponseWriter, r *http.Request) {
	body, err := io.ReadAll(r.Body)
	if err != nil {
		log.Fatal(err)
	}

	w.Write(body)
})
```

### Como Testar:

Use um software como **Insomnia** ou **Postman** para testar a rota.

## 6. Tratamento de Requisições para Rotas Inexistentes

Quando uma requisição é feita para uma rota inexistente, é realizado o redirecionamento para a rota principal. Para fazer a identificação, use:

```go
api.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
    path := r.URL.Path

    // Verifica se a requisição é para a rota principal
    if path == "/" {
        w.Write([]byte("Main route"))

        return
    }

    // Se a rota não existir, informa ao usuário
    w.Write([]byte(fmt.Sprintf("Route `%s` does not exist.", path)))
})
```

Neste exemplo, quando a requisição for direcionada à rota **/**, a resposta será **"Main route"**, caso a requisição seja para uma rota inexistente, a resposta será uma mensagem informando que a rota não existe.

### Como Testar:

Para testar o tratamento de rotas inexistentes, basta acessar uma URL que não esteja definida no servidor:

```sh
curl http://localhost:3030/nonexistent
```

A resposta esperada será: **"Route '/nonexistent' does not exist."**.

## 7. Redirecionamento

Se precisar redirecionar requisições de uma rota para outra, utilize **http.Redirect**, especificando a rota de destino e o código de status HTTP:

```go
api.HandleFunc("/main", func(w http.ResponseWriter, r *http.Request) {
	http.Redirect(w, r, "/home", http.StatusMovedPermanently)
})

api.HandleFunc("/home", func(w http.ResponseWriter, r *http.Request) {
	w.Write([]byte("Home route"))
})
```

### Como Testar:

Acesse a URL:

```sh
curl http://localhost:3030/main
```

Ao acessar a URL **"http://localhost:3030/main"**, a resposta será **"Home route"**, pois a rota **"/main"** redirecionou para **"/home"**.

## Considerações Finais

O **ServeMux** é uma ferramenta simples e eficiente para gerenciar rotas HTTP em Go. Ele permite definir rotas de maneira intuitiva e oferece funcionalidades para manipulação de parâmetros na URL, query parameters e corpo da requisição. Por ser uma solução nativa, o ServeMux é capaz de atender a grande parte das necessidades de roteamento nas aplicações, dispensando a necessidade de pacotes adicionais e tornando o desenvolvimento mais direto e integrado com o ecossistema Go.
