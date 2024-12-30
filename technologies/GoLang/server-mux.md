# ServeMux no GoLang

O `ServeMux` é um roteador HTTP básico do Go, utilizado para associar URLs a funções de tratamento (handlers). Este guia explica como configurar e usar o `ServeMux` para criar um servidor HTTP básico, sem a necessidade de bibliotecas externas de roteamento.

## 1. Criar o Servidor

Primeiramente, crie uma instância de `ServeMux` utilizando a função `http.NewServeMux()` do pacote `http`. Em seguida, use `http.ListenAndServe` para iniciar o servidor.

### Exemplo de Código:

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

Isso fará o servidor começar a escutar na URL `http://localhost:3030`.

## 2. Criar Rotas

Para criar uma rota, use o método `HandleFunc` da instância `ServeMux` que criou. O primeiro argumento é o método da rota e o caminho, e o segundo é a função de tratamento para a requisição.

### Exemplo de uma Rota Simples:

```go
api.HandleFunc("GET /user", func(w http.ResponseWriter, r *http.Request) {
	w.Write([]byte("User route"))
})
```

Ao acessar a rota `http://localhost:3030/user`, a resposta será "User route".

### Como Testar a Rota:

Abra um terminal e use o comando `curl`:

```sh
curl http://localhost:3030/user
```

## 3. Acessar Parâmetros na URL

Para capturar parâmetros dinâmicos de uma URL, defina a sua posição e nome. Para acessar os valores desses parâmetros, utilize o método `r.PathValue`.

### Exemplo de Rota com Parâmetro de URL:

```go
api.HandleFunc("/user/{id}", func(w http.ResponseWriter, r *http.Request) {
	userId := r.PathValue("id")  // Captura o valor do parâmetro {id} da URL

	w.Write([]byte(userId))
})
```

### Como Testar:

Acesse a URL com o parâmetro:

```sh
curl http://localhost:3030/user/123
```

A resposta será "123", que é o valor do parâmetro `{id}`.

## 4. Acessar Query Parameters na URL

Para acessar parâmetros de consulta (query parameters) da URL, use o método `r.URL.Query().Get("nome_do_parametro")`.

### Exemplo de Acesso a Query Parameters:

```go
api.HandleFunc("/user", func(w http.ResponseWriter, r *http.Request) {
	code := r.URL.Query().Get("code")  // Obtém o valor do query parameter "code"

	w.Write([]byte(code))
})
```

### Como Testar:

Acesse a URL com o parâmetro de consulta `code`:

```sh
curl "http://localhost:3030/user/123?code=abc"
```

A resposta será "abc", que é o valor do parâmetro `code`.

Obs.: Caso o parâmetro de consulta esteja ausente, o valor obtido será uma string vazia.

## 5. Acessar o Corpo da Requisição

Para acessar o corpo da requisição, use o pacote `io` para ler o conteúdo enviado. Exemplo:

```go
body, err := io.ReadAll(r.Body)
if err != nil {
	log.Fatal(err)
}
```

### Exemplo Completo:

```go
api.HandleFunc("/user", func(w http.ResponseWriter, r *http.Request) {
	body, err := io.ReadAll(r.Body)
	if err != nil {
		log.Fatal(err)
	}

	w.Write(body)
})
```

### Como Testar:

Use um software como `Insomnia` ou `Postman` para testar a rota.

## 6. Tratamento de Requisições para Rotas Inexistentes

Quando uma requisição é feita para uma rota inexistente, é feito o redirecionamento para `/`, a rota principal.

### Exemplo de Tratamento para Rota Inexistente:

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

Neste exemplo, quando a requisição for direcionada à rota `/`, a resposta será "Main route", caso a requisição seja para uma rota inexistente, a resposta será uma mensagem informando que a rota não existe, como: `Route '/nonexistent' does not exist.`.

### Como Testar:

Para testar o tratamento de rotas inexistentes, basta acessar uma URL que não esteja definida no servidor:

```sh
curl http://localhost:3030/nonexistent
```

A resposta esperada será: `Route '/nonexistent' does not exist.`.

## 7. Redirecionamento

Se você precisar redirecionar requisições de uma rota para outra, utilize `http.Redirect`, especificando a rota de destino e o código de status HTTP.

### Exemplo de Redirecionamento:

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

Ao acessar a URL `http://localhost:3030/main`, a resposta será "Home route", pois a rota `/main` está redirecionando para `/home`.

## Considerações Finais

O `ServeMux` é uma ferramenta simples e eficiente para gerenciar rotas HTTP em Go. Ele permite definir rotas de maneira intuitiva e oferece funcionalidades para manipulação de parâmetros na URL, query parameters e corpo da requisição.
