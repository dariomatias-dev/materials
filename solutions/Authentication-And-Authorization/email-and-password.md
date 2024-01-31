# Sistema de Autenticação e Autorização

A autenticação e autorização são componentes críticos em qualquer aplicação, independentemente das tecnologias utilizadas. Este projeto visa explicar uma abordagem unificada e adaptável, permitindo que desenvolvedores tenham uma base para adicionar as funcionalidades de autenticação e autorização em suas aplicações, independentemente da pilha tecnológica escolhida.

Existe várias abordagens de implementação e esse projeto abordará a com e-mail e senha, por essa razão aconselho a pesquisar outras soluções para que possa aprender mais sobre e desenvolver a sua própria solução.

## Rotas

As explicações serão voltadas para uma API que possui as seguintes rotas:

#### Rotas de autenticação

**POST (login)**: Login
**GET (refresh)**: atualização dos tokens
**Post (validate-email)**: validação de email

#### Rotas de usuário

**POST (user)**: criação de usuário
**GET (user/:id)**: obtenção de usuário
**GET (users)**: obtenção dos usuários
**PATCH (user/:id)**: atualização de usuário
**DELETE (user/:id)**: exclusão de usuário

### Tipos de rotas

As rotas serão divididas em dois tipos: **públicas** e **privadas**.
Nas públicas será possível acessar sem a necessidade de um token de acesso, enquanto nas privadas será necessário possuí-lo.

**Rotas públicas**: criação de conta, Login, Refresh e Validação de Email.
**Rotas privadas**: todas as rotas que não foram citadas acima.

## Cargos

Para acessar determinadas rotas será preciso que o usuário possua determinados cargos, mas, por quais razões? Pelas seguintes e outras:

- A rota “_users_” retorna os dados de todos os usuários, seria adequado que qualquer pessoa tivesse acesso a essa rota? Certamente não, por isso essa rota deve ser uma rota administrativa, que apenas os administradores do servidor devem ter acesso.

- Em um e-commerce haverá uma rota onde os produtos que serão exibidos são adicionados, sendo assim, qualquer pessoa pode conseguir acessar essa rota? Não, apenas os usuários que tiverem determinado cargo como parceiro por exemplo, devem conseguir ter acesso a essa rota, pois para conseguir esse cargo esses usuários devem passar por verificações para diminuir a chance de algo impróprio ser postado, o que não é possível com usuários não verificados, e que pode comprometer todo o negócio caso algo desse tipo aconteça.

### Tipos de cargos

Para esse projeto, iremos ter os seguintes cargos: usuário (**user**) e administrador (**admin**).

As contas que possuírem o cargo de administrador não irão conseguir acessar rotas administrativas, enquanto as que possuírem terão acesso a todas as rotas da aplicação.
Esses cargos devem ser atribuídos pelo servidor, não sendo possível obtê-los através de uma requisição, visto que dessa forma uma conta poderia terminar recebendo um cargo que não deveria possuir.

## Criação de conta

A conta terá os seguintes campos: nome (name), idade (age), email, email válido (valid_email), senha (password), cargos (roles), data de criação (created_at) e data de atualização (updated_at).

SQL da tabela de usuários:

```sql
CREATE TABLE
    IF NOT EXISTS "users" (
        id UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
        name VARCHAR(128) UNIQUE NOT NULL,
        age INTEGER NOT NULL,
        email TEXT UNIQUE NOT NULL,
        valid_email BOOLEAN DEFAULT FALSE,
        password TEXT NOT NULL,
        roles TEXT[] NOT NULL,
        created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP NOT NULL,
        updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP NOT NULL
)
```

Abaixo está o passo a passo de como deve ser a criação de uma conta:

1 - Preenchimento do formulário de criação de conta:

- Usuário acessa formulário de criação de conta.
- Todos os campos são validados para garantir o correto preenchimento.
- Caso os dados sejam válidos permite o envio do formulário.

2 - Criacão da conta pelo servidor:

- Servidor revalida os dados enviados.
- Verifica se o e-mail já está cadastrado, caso esteja retorna status 409 e informa que o e-mail já está cadastrado, caso não, é criado um novo usuário com status de e-mail não verificado.
- A senha é criptografada com uma biblioteca de criptografia (geralmente a biblioteca se chama bcrypt).
- Cargo “user” é atribuído a conta.
- A conta é criada na tabela de usuários juntamente com a tabela de tokens com os campos _access_token_ e _refresh_token_ vazios.

Obs.: Se quiser, pode criar a tabela de tokens quando o usuário fizer o primeiro login, no entanto, esteja atento, será necessário verificar se já existe uma tabela de tokens vinculada ao usuário para atualizá-la ao gerar novos tokens e, caso não exista, terá que criá-la.
Optei pela abordagem de criá-la quando a conta é criada porquê dessa forma posso simplesmente atualizá-la sem a necessidade de fazer verificações.

### Verificação do e-mail:

Ao criar uma conta é importante verificar se o endereço de e-mail fornecido é válido, ou seja, existe.
Existem muitas formas de fazer essa verificação, como SMS, acessar um link e dentre outros.
A abordagem que utilizaremos será através de um código de validação enviado para o e-mail fornecido.

SQL da tabela:

```sql
CREATE TABLE
    IF NOT EXISTS "email_validations" (
        user_id UUID PRIMARY KEY,
        verification_code VARCHAR(6) NOT NULL,
        expiration_time INTEGER NOT NULL,
        created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP NOT NULL
    )
```

Passo a passo:

1 - Geração do código de validação de e-mail:

- Código de 6 dígitos aleatórios é gerado pelo servidor.
- Objeto de registro de validação de e-mail é criado com ID do usuário criado, vinculado ao código gerado e com tempo de expiração de 15 minutos.
- Registro criado salvo na tabela de validação de e-mail.
- Servidor envia e-mail para o endereço fornecido, contendo o código gerado e retorna ao site status 200, que redireciona para a página onde o usuário deverá inserir o código de validação.

2 - Envio do código para o servidor:

- Usuário obtém código do e-mail enviado, insere no formulário de validação de e-mail e envia.

3 - Validação do e-mail:

- Servidor realiza busca no banco de dados com base no código enviado para verificar se existe um registro vinculado ao código.
- Se o registro existir, é verificado se o tempo de expiração é menor que o horário da validação, se for, retorna uma mensagem de erro indicando tempo expirado (
  time expired), caso não, o status do e-mail do usuário passa a ser verificado, e é retornada uma mensagem indicando que o e-mail foi verificado com sucesso.
- Servidor exclui registro do código verificado e cria log de e-mail verificado vinculado ao ID do usuário.

## Login

1 - Formulário de Login:

- Usuário acessa página de login da aplicação, preenche o formulário de Login com as suas credenciais (e-mail e senha) e envia os dados para o servidor.

2 - Geração dos tokens:

- Servidor revalida os dados enviados.
- Verifica se há um usuário com o e-mail fornecido.
- Caso o usuário seja valido, verifique se o número de tentativas de Login é igual a 10, se for, verifica se a diferença entre o horário atual e da última tentativa de Login é um valor positivo, se for, retorne status 401 e mensagem de erro: sua conta foi temporariamente bloqueada devido a várias tentativas de login malsucedidas. Aguarde [quantidade de horas] horas antes de tentar novamente. Se os problemas persistirem, entre em contato com o suporte (your account has been temporarily blocked due to multiple unsuccessful login attempts. Please wait for [quantidade de horas] hours before trying again. If issues persist, contact support).
- Se a diferença for negativa, significa que as 24 horas se passaram, então pode resetar a quantidade de tentativas de Login para 0.
- Verifique se a senha enviada e a do usuário são iguais, caso sejam retorne os tokens **access_token** e **refresh_token**, caso não, incremente 1 no campo **attempts** da tabela de tentativas de Login vinculada ao usuário e retorne status 401 informando e-mail ou senha inválidos (invalid email or password).

### Número de Tentativas e Bloqueio:

É necessário que seja implementado um sistema de bloqueio quando as tentativas chegarem a determinado número, pois existe o risco de alguém tentar descobrir as credenciais de acesso (e-mail e senha) de um usuário por meio da tentativa e erro, chamado de ataque de força bruta.

Por essa razão, ao colocar um número máximo de tentativas de Login será preciso esperar determinado tempo até poder fazer Login novamente quando o limite for alcançado, o que resulta em uma maior segurança para os usuários, contudo, esteja ciente que existe outros estratégias mais seguras que essa, e que aconselho a utilizar, porém são mais trabalhosas de implementar.

SQL da tabela:

```sql
CREATE TABLE
    IF NOT EXISTS "login_attempts" (
        user_id UUID PRIMARY KEY,
        attempts INTEGER DEFAULT 0 NOT NULL,
        last_failed_login_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP NOT NULL,
        FOREIGN KEY(user_id) REFERENCES users(id)
        )
```

## Refresh

- Obtém refresh_token enviado na requisição.
- Verifica se o token é do tipo refresh_token.
- Extrai objeto payload do token com os dados do usuário.
- Verifica se o tempo de expiração do token é menor que o horário da verificação, caso seja retorna status 401 e informa que o token expirou, sendo necessário realizar Login.
- Se o token não tiver expirado, busque os tokens do usuário que corresponde ao ID que está no payload.
- Verifica se o refresh_token enviado na requisição é igual ao atual refresh_token do usuário obtido.
- Se for igual retorna status 200 e gere novos tokens, se for diferente retorna status 401 informando token inválido (invalid token).

## Middlewares

### Validar ID da URL da requisição

- Obtenha o ID dos parâmetros da requisição.
- Converta/Verifique se o ID é um UUID válido.
- Verifique se o UUID é da versão 4.
- Caso não seja válido retorne mensagem de erro informando que o ID enviado não é válido.

Esse middleware será usado em todas as rotas que tenham “:id”, mas pode adaptá-lo para validar IDs que são do tipo UUIDs mas não se chamam “:id”, como por exemplo “:product-id".

### Verificação de token de acesso

Esse middleware será colocado no ínicio de todas as rotas onde é necessário possuir o token de acesso, pois irá validar se o token que está na requisição é válido.

Passo a passo:

- Token é obtido da requisição.
- Dados que estão no token são extraídos.
- É verificado se o token é do tipo de acesso, caso não seja retorna status 401 e mensagem: token inválido (Invalid token).
- É verificado se o tempo de expiração do token não é menor que o horário da verificação, caso seja é retornado status 401 e mensagem: token expirado (Expired token).
- Caso o token seja válido, os dados obtidos são anexados a requisição para poderem ser usados pelo que vier depois.

### Verificar se é o usuário tentando acessar os seus dados:

Esse middleware deve vir depois do middleware que verifica se há um token de acesso válido, pois ele faz uso dos dados que são extraídos do token.

Passo a passo:

- Obtenha o ID do usuário da requisição.
- Obtenha o ID do usuário que foi extraído do token.
- Obtenha os cargos do usuário que foi extraído do token.
- Adicione uma condição para saber se o usuário possui um cargo de administrador ou equivalente, lhe dando passe livre para acessar a rota.
- Caso passe pela primeira verificação, verifique se o ID da requisição é diferente do ID do token, caso seja significa que é alguém tentando acessar dados que não lhes pertencem, nesse caso será retornado status 401 com a mensagem: Você não tem permissão para acessar os dados de outro usuário (You do not have permission to access another user's data). Caso sejam iguais.

## Tokens

Os tokens serão de dois tipos: de acesso (access_token) e atualização (refresh).

A tabela de usuários irá ter: token de acesso, token de atualização, ID do usuário que está vinculada e data de quando foi atualizada. Não será necessário a data de quando foi criada, pois será no mesmo momento que a conta de usuário for criada.

Todas as vezes que forem gerados novos tokens a tabela de tokens vinculada ao usuário será atualizada com os novos tokens, sendo importante para informar quais são os tokens atuais da conta e automaticamente invalidando os tokens anteriores.

SQL da tabela de tokens:

```sql
CREATE TABLE
    IF NOT EXISTS "tokens" (
        user_id VARCHAR(36) UNIQUE NOT NULL,
        access_token TEXT DEFAULT '' NOT NULL,
        refresh_token TEXT DEFAULT '' NOT NULL,
        updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
        FOREIGN KEY(user_id) REFERENCES users(id)
)
```

### Token de acesso:

- Tempo de expiração de 1 dia.
- Será apenas usado para acessar as rotas, indicando que o usuário está autenticado.
- Ao expirar será necessário fazer Login novamente ou usar token de atualização para obter um novo.

### Token de atualização:

- Tempo de expiração de uma semana (7 dias).
- Será apenas usado para obter novos tokens sem a necessidade de realizar um novo Login, que necessita informar a senha e e-mail da conta.
- Ao expirar será necessário realizar Login.

### Payload

O payload terá os seguintes dados:

- ID do usuário
- Cargos que possui
- Tipo do token
- Tempo de expiração

## Códigos de status

- **200 OK**: A solicitação foi bem-sucedida. Não há problemas ou erros a serem relatados, tudo ocorreu conforme o esperado.

- **201 Created**: A solicitação foi bem-sucedida e um novo recurso foi criado com êxito. É usada quando a criação de um registro ocorreu sem problemas.

- **401 Unauthorized**: A solicitação não foi autorizada. Isso ocorre quando o usuário não possui as credenciais adequadas para acessar a rota ou recurso solicitado.

- **409 Conflict**: A solicitação não pôde ser concluída devido a um conflito com o estado atual do recurso alvo. É usado quando há um conflito entre os dados enviados na solicitação e os dados existentes no banco de dados.
