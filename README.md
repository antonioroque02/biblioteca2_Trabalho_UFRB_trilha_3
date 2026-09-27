# API de Biblioteca (ex06)

API REST para gerenciamento de livros e registro de empréstimos. O projeto usa Node.js, Express 5, Prisma ORM, PostgreSQL e Zod.
## Funcionalidades

- Listar livros com filtros, paginação e ordenação; consultar por ID.
- Criar, atualizar e remover livros, associando-os a autores e gêneros.
- Registrar empréstimos com validação de estudante, pendências, limite de empréstimos ativos e disponibilidade de exemplares.
- Validar corpos e parâmetros e responder erros de domínio e do Prisma com códigos HTTP apropriados.
## Requisitos

- Node.js 20 ou superior e npm.
- PostgreSQL em execução e um banco criado para a aplicação.
## Configuração

1. Clone o repositório e entre na pasta do projeto:

  ```powershell
  git clone https://github.com/SEU_USUARIO/api-biblioteca.git
  Set-Location api-biblioteca
  ```
2. Crie o arquivo local de ambiente e configure a URL do PostgreSQL:

  ```powershell
  Copy-Item .env.example .env
  ```

  Edite `.env`:

  ```env
  DATABASE_URL="postgresql://USUARIO:SENHA@localhost:5432/biblioteca"
  ```

  Crie o banco `biblioteca` no PostgreSQL ou ajuste o nome na URL.
3. Instale as dependências, aplique as migrations e, se desejar, carregue os dados de exemplo:

  ```powershell
  npm install
  npx prisma migrate deploy
  npm run db:seed
  ```

  **Atenção:** o seed apaga os registros existentes das tabelas da biblioteca antes de recriar os dados de exemplo. Não o execute em um banco com dados que deseja preservar.
4. Inicie a API:

  ```powershell
  npm run dev
  ```

  A API escuta em `http://localhost:3000`. Use `npm start` para iniciar sem o Nodemon.
## Endpoints

| Método | Endpoint | Descrição |
| --- | --- | --- |
| GET | `/livros` | Lista livros. Aceita `titulo`, `genero`, `pagina`, `limite`, `ordenar` e `direcao`. |
| GET | `/livros/:id` | Consulta um livro pelo ID. |
| POST | `/livros` | Cadastra um livro. |
| PUT | `/livros/:id` | Atualiza um livro. |
| DELETE | `/livros/:id` | Remove um livro. |
| POST | `/emprestimos` | Registra um empréstimo. |
### Exemplo: criar livro

Envie para `POST /livros`:

```json
{
  "titulo": "Dom Casmurro",
  "autor": "Machado de Assis",
  "isbn": "9788508058859",
  "ano": 1899,
  "generoId": 1
}
```

`titulo` e `autor` são obrigatórios; `isbn`, `ano` e `generoId` são opcionais.
### Exemplo: registrar empréstimo

Envie para `POST /emprestimos` com `Content-Type: application/json`. Os IDs devem ser números JSON, e não textos entre aspas:

```json
{
  "livroId": 1,
  "estudanteId": 1,
  "dias": 14
}
```

`livroId` e `estudanteId` são obrigatórios. `dias` é opcional e aceita valores inteiros de 1 a 30; o padrão é 14. O empréstimo só é registrado para estudante ativo, sem devoluções atrasadas, com menos de três empréstimos ativos e quando há exemplares disponíveis.
### Exemplo: listar livros

```text
GET http://localhost:3000/livros?titulo=dom&pagina=1&limite=10&ordenar=titulo&direcao=asc
```

`ordenar` aceita `id`, `titulo`, `ano`, `criadoEm` ou `atualizadoEm`. `direcao` aceita `asc` ou `desc`.
## Erros HTTP

- `400`: dados ou parâmetros inválidos, ou relacionamento inválido.
- `404`: registro não encontrado.
- `409`: conflito, como valor único duplicado ou regra de empréstimo não atendida.
- `500`: erro inesperado no servidor.
## Insomnia

A coleção Postman compatível com importação no Insomnia está em `docs/biblioteca-api.postman_collection.json`. Ela contém exemplos de operações de livros; para empréstimos, crie uma requisição `POST` para `http://localhost:3000/emprestimos` usando o JSON acima. Os IDs devem existir no banco atual.
## Comandos úteis

| Comando | Uso |
| --- | --- |
| `npm run dev` | Inicia a API com Nodemon. |
| `npm start` | Inicia a API. |
| `npx prisma migrate deploy` | Aplica migrations pendentes. |
| `npm run db:seed` | Substitui os dados pelas amostras do seed. |
| `npx prisma generate` | Gera o Prisma Client. |
## Estrutura

```text
app.js                 inicialização da API
controllers/           controladores HTTP
data/                  repositórios Prisma
docs/                  documentação e coleção de requisições
dtos/                  formatos de resposta
errors/                erros de domínio
middlewares/           validação e tratamento de erros
models/                criação de entidades de domínio
prisma/                schema, migrations e seed
routes/                rotas Express
schemas/               validações Zod
services/              regras de negócio
```

## Segurança

O arquivo `.env` contém credenciais e não deve ser enviado ao GitHub. O repositório inclui `.env.example` como modelo; preencha o `.env` localmente com os dados do seu ambiente.
# API de Biblioteca

API REST para gerenciamento de livros de uma biblioteca. O projeto usa Node.js, Express, Prisma ORM, PostgreSQL e Zod.

## Recursos

- Cadastro, consulta, atualização e remoção de livros.
- Associação de livros a autores e gêneros.
- Busca por título e gênero, paginação e ordenação.
- Validação dos dados recebidos e respostas de erro padronizadas.
- Documentação interativa com Swagger.
- Seed com dados de exemplo para a biblioteca.

## Tecnologias

- Node.js e Express 5
- PostgreSQL e Prisma ORM
- Zod
- Swagger UI

## Pré-requisitos

- Node.js 20 ou superior
- PostgreSQL em execução
- npm

## Instalação

```bash
git clone https://github.com/SEU_USUARIO/api-biblioteca.git
cd api-biblioteca
npm install
```

Crie o arquivo `.env` a partir do exemplo e informe as credenciais do PostgreSQL:

```bash
copy .env.example .env
```

```env
DATABASE_URL="postgresql://USUARIO:SENHA@localhost:5432/biblioteca"
```

Depois, aplique as migrations e carregue os dados de exemplo:

```bash
npx prisma migrate deploy
npx prisma db seed
```

Para desenvolvimento, execute:

```bash
npm run dev
```

A API estará disponível em `http://localhost:3000`.

## Documentação da API

Com o servidor em execução, abra `http://localhost:3000/api-docs` para acessar o Swagger UI.

Também há uma coleção para Postman/Insomnia em `docs/biblioteca-api.postman_collection.json`.

## Endpoints

| Método | Endpoint | Descrição |
| --- | --- | --- |
| GET | `/livros` | Lista livros com filtros, paginação e ordenação. |
| GET | `/livros/:id` | Busca um livro pelo identificador. |
| POST | `/livros` | Cria um livro e o associa a um autor. |
| PUT | `/livros/:id` | Atualiza os campos informados de um livro. |
| DELETE | `/livros/:id` | Remove um livro. |

### Parâmetros de listagem

| Parâmetro | Tipo | Padrão | Observação |
| --- | --- | --- | --- |
| `titulo` | texto | - | Filtra parte do título, sem diferenciar maiúsculas/minúsculas. |
| `genero` | texto | - | Filtra pelo nome do gênero. |
| `pagina` | inteiro | `1` | Deve ser maior que zero. |
| `limite` | inteiro | `10` | De `1` a `100`. |
| `ordenar` | texto | `titulo` | `id`, `titulo`, `ano`, `criadoEm` ou `atualizadoEm`. |
| `direcao` | texto | `asc` | `asc` ou `desc`. |

Exemplo:

```text
GET /livros?titulo=dom&pagina=1&limite=10&ordenar=titulo&direcao=asc
```

### Criar um livro

```json
{
  "titulo": "Dom Casmurro",
  "autor": "Machado de Assis",
  "isbn": "9788508058859",
  "ano": 1899,
  "generoId": 1
}
```

`titulo` e `autor` são obrigatórios. `isbn`, `ano` e `generoId` são opcionais.

## Respostas de erro

- `400`: parâmetros ou dados inválidos.
- `404`: livro não encontrado.
- `409`: valor único já cadastrado, como ISBN duplicado.
- `500`: erro inesperado no servidor.

## Scripts

| Comando | Descrição |
| --- | --- |
| `npm start` | Inicia a API. |
| `npm run dev` | Inicia com reinicialização automática. |
| `npx prisma generate` | Gera o Prisma Client. |
| `npx prisma migrate deploy` | Aplica migrations pendentes. |
| `npx prisma db seed` | Popula o banco com dados de exemplo. |

## Estrutura

```text
app.js                 ponto de entrada da aplicação
controllers/           tratamento das requisições HTTP
data/                  acesso ao banco com Prisma
middlewares/           validações e tratamento de entrada
prisma/                schema, migrations e seed
routes/                definição das rotas
schemas/               regras de validação com Zod
docs/                  Swagger e coleção Postman
```

## Publicação no GitHub

Após criar um repositório vazio no GitHub, associe-o ao projeto:

```bash
git remote add origin https://github.com/SEU_USUARIO/api-biblioteca.git
git branch -M main
git push -u origin main
```

O arquivo `.env` não é versionado: nunca publique usuário, senha ou URL real do banco de dados.
