# ServeMux no GoLang

O `ServeMux` é um roteador básico em Go, utilizado para associar URLs a funções de tratamento. Este guia explica como configurar e usar o `ServeMux` para criar um servidor HTTP básico sem a necessidade de utilizar pacotes de roteamento.

## 1. Criar o Servidor

Primeiro, crie uma instância de `ServeMux` utilizando a função `http.NewServeMux()` do pacote `http`. Em seguida, use `http.ListenAndServe()` para iniciar o servidor.

### Código Exemplo:

```go
package main

import (
	"fmt"
	"net/http"
	"log"
)

func main() {
	// Criação da instância ServeMux
	api := http.NewServeMux()

	// Configuração do servidor para escutar na porta 3030
	fmt.Println("Server listening on http://localhost:3030")
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

Para criar uma rota, use o método `HandleFunc` da instância `ServeMux`. O primeiro argumento é o caminho da rota, e o segundo é a função de tratamento para a requisição.

### Exemplo de uma Rota Simples:

```go
api.HandleFunc("/user", func(w http.ResponseWriter, r *http.Request) {
	w.Write([]byte("User route"))
})
```

Com isso, quando você acessar `http://localhost:3030/user`, a resposta será "Main route".

### Como Testar a Rota:
Abra um terminal e use o comando `curl`:

```sh
curl http://localhost:3030/user
```

## 3. Acessar Parâmetros na URL

Para capturar parâmetros dinâmicos de uma URL, defina parâmetros na rota. Para acessar os valores desses parâmetros, utilize o método `r.URL.Path`.

### Exemplo de Rota com Parâmetro de URL:

```go
api.HandleFunc("/user/{id}", func(w http.ResponseWriter, r *http.Request) {
	userId := r.URL.Path[7:]  // Captura o valor do parâmetro {id} da URL

	w.Write([]byte(userId))
})
```

### Como Testar:

Acesse a URL com o parâmetro:

```sh
curl http://localhost:3030/user/123
```

Ao acessar a URL `http://localhost:3030/user/123`, a resposta será "123".

## 4. Acessar Query Parameters na URL

Para acessar parâmetros de consulta (query parameters) da URL, use o método `r.URL.Query().Get("nome_do_parametro")`.

### Exemplo de Acesso a Query Parameters:

```go
api.HandleFunc("/user/{id}", func(w http.ResponseWriter, r *http.Request) {
	code := r.URL.Query().Get("code")  // Obtém o valor da query parameter "code"

	w.Write([]byte(code))
})
```

### Como Testar:

Acesse a URL com o parâmetro de consulta `code`:

```sh
curl "http://localhost:3030/user/123?code=abc"
```

A resposta será "abc", que é o valor do parâmetro `code`.

## 5. Tratamento de Requisições para Rotas Inexistentes

Quando uma requisição é realizada para uma rota que não existe, é possível verificar o caminho da URL para determinar se a requisição se refere à rota principal ou a uma rota inexistente. Assim, é possível aplicar o tratamento adequado para cada caso.

### Exemplo de Tratamento para Rota Inexistente:

```go
api.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
    path := r.URL.Path

    if path == "/" {
        w.Write([]byte("Main route"))
        return
    }

    w.Write([]byte(fmt.Sprintf("Route `%s` does not exist.", path)))
})
```

### Comportamento Esperado:

- Se a requisição for para a rota principal `/`, a resposta será "Main route".
- Se a requisição for feita para uma rota inexistente, a resposta será uma mensagem informando que a rota não existe, por exemplo: `Route '/some/path' does not exist.`.

### Como Testar:

Para testar o tratamento de rotas inexistentes, você pode acessar uma URL que não exista, como no exemplo abaixo:

```sh
curl http://localhost:3030/nonexistent
```

A resposta será: `Route '/nonexistent' does not exist.`.


# Considerações Finais

O `ServeMux` oferece uma maneira simples e eficiente de lidar com rotas HTTP em Go. Ele permite que você defina rotas de maneira intuitiva e faça o tratamento de parâmetros na URL e nas queries de maneira prática. Para servidores mais complexos, você pode explorar outros frameworks ou bibliotecas de roteamento, mas o `ServeMux` é suficiente para muitas aplicações simples.
