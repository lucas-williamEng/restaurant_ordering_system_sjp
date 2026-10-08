# 🍽️ Restaurant Ordering System API

API REST para um **sistema de autoatendimento de restaurante**, responsável por gerenciar o cardápio digital (categorias e produtos) que será consumido por totens, aplicativos ou painéis de pedido.

> Projeto da disciplina de **Desenvolvimento Back-End** — 4º Período de **Engenharia de Software** — Campus São José dos Pinhais (SJP).

---

## 1. Nome e descrição do projeto

**Nome:** `restaurant_ordering_system` (Restaurant Ordering System API)

**Problema que busca resolver:** restaurantes que adotam o autoatendimento precisam de uma fonte única e organizada de dados do cardápio. Sem isso, a atualização de preços, a inclusão de novos itens e a organização por seções (pizzas, bebidas, sobremesas etc.) ficam manuais e sujeitas a inconsistências entre os pontos de venda.

**Contexto:** a aplicação é o back-end de um sistema de autoatendimento. O cliente navega pelas categorias, escolhe os produtos e (em evoluções futuras) monta o pedido. Este serviço é a base de dados do cardápio, exposta por meio de uma API.

**Objetivo da API:** oferecer operações de CRUD (criar, listar, consultar, atualizar e remover) para **categorias** e **produtos**, persistindo os dados em um banco PostgreSQL gerenciado pelo Supabase, de forma que qualquer front-end possa consumir e administrar o cardápio.

---

## 2. Identificação do estudante

| Campo | Informação |
|-------|------------|
| **Nome completo** | Lucas William Santos Rodrigues |
| **Disciplina** | Desenvolvimento Back-End |
| **Curso** | Engenharia de Software — 4º Período |
| **Instituição / Campus** | SJP — São José dos Pinhais |

---

## 3. Tecnologias utilizadas

| Tecnologia | Versão | Uso no projeto |
|------------|--------|----------------|
| **Node.js** | 20.6+ (recomendado 22 LTS ou superior) | Ambiente de execução (precisa suportar `--env-file`) |
| **TypeScript** | ^7.0 | Tipagem estática e compilação para JavaScript |
| **Express** | ^5.2 | Framework HTTP, rotas e middlewares |
| **Supabase** (`@supabase/supabase-js`) | ^2.115 | Cliente de acesso ao banco e à plataforma |
| **PostgreSQL** | (gerenciado pelo Supabase) | Banco de dados relacional |
| **tsx** | ^4.23 | Execução de TypeScript em desenvolvimento |
| **Git / GitHub** | — | Controle de versão |
| **Postman** | — | Testes manuais dos endpoints |

---

## 4. Entidades e relacionamentos

O sistema possui duas entidades principais: **Category** (categoria) e **Product** (produto).

### Category (Categoria)

Agrupa produtos em seções do cardápio.

| Atributo | Tipo | Descrição |
|----------|------|-----------|
| `id` | UUID | Identificador único (gerado pelo banco) |
| `name` | texto (até 100) | Nome da categoria (ex.: "Pizzas") |
| `description` | texto (até 255) | Descrição da categoria |
| `icon` | texto (até 10) | Emoji/ícone de exibição (ex.: 🍕) |
| `display_order` | inteiro | Posição de exibição no cardápio |
| `active` | booleano | Indica se a categoria está visível/ativa (padrão: `true`) |
| `created_at` / `updated_at` | timestamp | Datas de criação e última atualização |

### Product (Produto)

Item vendido no restaurante, sempre vinculado a uma categoria.

| Atributo | Tipo | Descrição |
|----------|------|-----------|
| `id` | UUID | Identificador único (gerado pelo banco) |
| `category_id` | UUID | Categoria à qual o produto pertence (chave estrangeira) |
| `title` | texto (até 150) | Nome do produto |
| `description` | texto (até 500) | Descrição do produto |
| `price` | decimal(10,2) | Preço de venda |
| `image` | texto (até 255) | URL/caminho da imagem do produto (opcional) |
| `available` | booleano | Produto disponível para pedido no momento (padrão: `true`) |
| `active` | booleano | Produto ativo/visível no cardápio (padrão: `true`) |
| `created_at` / `updated_at` | timestamp | Datas de criação e última atualização |

### Relacionamento

```
┌──────────────┐ 1                  N ┌──────────────┐
│   Category   │──────────────────────│   Product    │
└──────────────┘  possui / pertence a └──────────────┘
```

- **Uma categoria possui vários produtos** (1:N).
- **Cada produto pertence a exatamente uma categoria**, referenciada por `category_id`.

---

## 5. Estrutura do projeto

```
restaurant_ordering_system/
├── src/
│   ├── config/          # Configurações da aplicação (ex.: cliente Supabase)
│   ├── controllers/     # Recebem a requisição HTTP, validam e devolvem a resposta
│   ├── models/          # Tipos/interfaces das entidades (Category, Product)
│   ├── repositories/    # Acesso ao banco de dados (consultas ao Supabase)
│   ├── routes/          # Definição das rotas e ligação com os controllers
│   ├── app.ts           # Criação e configuração do app Express
│   └── server.ts        # Ponto de entrada: inicia o servidor HTTP
├── dist/                # Código JavaScript compilado (gerado pelo build, não versionado)
├── .env                 # Variáveis reais (NÃO versionado)
├── .env.example         # Modelo das variáveis necessárias
├── package.json
└── tsconfig.json
```

**Fluxo de uma requisição:** `routes` → `controllers` → `repositories` → Supabase/PostgreSQL.

---

## 6. Configuração e execução

### Pré-requisitos

- [Node.js](https://nodejs.org/) 20.6 ou superior
- [Git](https://git-scm.com/)
- Um projeto criado no [Supabase](https://supabase.com/)

### Passo a passo

```bash
# 1. Clonar o repositório
git clone https://github.com/lucas-williamEng/restaurant_ordering_system.git
cd restaurant_ordering_system

# 2. Instalar as dependências
npm install

# 3. Criar o arquivo de variáveis de ambiente a partir do modelo
cp .env.example .env        # no Windows (PowerShell): copy .env.example .env
# edite o .env e informe suas credenciais do Supabase

# 4. Criar as tabelas no Supabase (veja a seção 8)

# 5. Iniciar em modo desenvolvimento (com reinício automático)
npm run dev
```

A API ficará disponível em **http://localhost:3000**.

### Scripts disponíveis

| Comando | Descrição |
|---------|-----------|
| `npm run dev` | Executa `src/server.ts` com `tsx`, `--watch` e carregamento do `.env` |
| `npm run build` | Compila o TypeScript para `dist/` |
| `npm start` | Executa a versão compilada (`dist/server.js`) |

Para rodar em modo produção:

```bash
npm run build
npm start
```

---

## 7. Variáveis de ambiente

O projeto lê as variáveis de um arquivo `.env` na raiz. **Nunca** versione esse arquivo. Use o `.env.example` como modelo:

```env
SUPABASE_URL=https://seu-projeto.supabase.co
SUPABASE_SECRET_KEY=sua-chave-secreta-aqui
```

| Variável | Obrigatória | Descrição |
|----------|:-----------:|-----------|
| `SUPABASE_URL` | Sim | URL do projeto Supabase (*Project Settings → API*) |
| `SUPABASE_SECRET_KEY` | Sim | Chave secreta (service role) do Supabase. **Dá acesso total ao banco — nunca exponha no front-end nem no Git** |

> ⚠️ **Segurança:** garanta que o `.gitignore` contenha a linha `.env`. Se a chave já foi enviada ao GitHub em algum commit, gere uma nova no painel do Supabase.

---

## 8. Banco de dados

O banco é um PostgreSQL hospedado no Supabase. Para reproduzir a estrutura, abra o **SQL Editor** do seu projeto Supabase e execute o script abaixo.

```sql
-- Categorias do cardápio
create table public.categories (
  id uuid not null default gen_random_uuid (),
  name character varying(100) not null,
  description character varying(255) null,
  icon character varying(10) null,
  display_order integer not null,
  active boolean not null default true,
  created_at timestamp with time zone null default now(),
  updated_at timestamp with time zone null default now(),
  constraint categories_pkey primary key (id)
) TABLESPACE pg_default;

-- Produtos do cardápio
create table public.products (
  id uuid not null default gen_random_uuid (),
  category_id uuid not null,
  title character varying(150) not null,
  description character varying(500) null,
  price numeric(10, 2) not null,
  image character varying(255) null,
  available boolean not null default true,
  active boolean not null default true,
  created_at timestamp with time zone null default now(),
  updated_at timestamp with time zone null default now(),
  constraint products_pkey primary key (id),
  constraint fk_products_category foreign key (category_id) references categories (id)
) TABLESPACE pg_default;
```

> A criação de `products` exige que `categories` já exista, por isso execute o script na ordem apresentada. A chave estrangeira impede excluir uma categoria que ainda possua produtos.

### Tabelas e relacionamento

| Tabela | Chave primária | Chave estrangeira | Relacionamento |
|--------|----------------|-------------------|----------------|
| `categories` | `id` | — | 1 categoria → N produtos |
| `products` | `id` | `category_id` → `categories.id` | N produtos → 1 categoria |

---

## 9. Documentação dos endpoints

URL base: `http://localhost:3000`

### API

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| GET | `/` | Rota inicial (verifica se a API está no ar) |

### Categorias

| Método | Endpoint | Descrição | Dados necessários |
|--------|----------|-----------|-------------------|
| GET | `/categories` | Lista todas as categorias | — |
| GET | `/categories/:id` | Consulta uma categoria pelo ID | `id` (UUID) na URL |
| GET | `/categories/search?keyword=` | Busca categorias pela palavra-chave informada | Query string `keyword` (ex.: `/categories/search?keyword=geladas`) |
| POST | `/categories` | Cadastra uma categoria | Body JSON: `name`, `description`, `icon`, `display_order`, `active` |
| PUT | `/categories/:id` | Atualiza uma categoria | `id` na URL + Body JSON com os campos a alterar |
| DELETE | `/categories/:id` | Remove uma categoria | `id` na URL |

### Produtos

| Método | Endpoint | Descrição | Dados necessários |
|--------|----------|-----------|-------------------|
| GET | `/products` | Lista todos os produtos | — |
| GET | `/products/:id` | Consulta um produto pelo ID | `id` (UUID) na URL |
| POST | `/products` | Cadastra um produto | Body JSON: `category_id`, `title`, `price` (obrigatórios); `description`, `image`, `available`, `active` (opcionais) |
| PUT | `/products/:id` | Atualiza um produto | `id` na URL + Body JSON com os campos a alterar |
| DELETE | `/products/:id` | Remove um produto | `id` na URL |

> Todas as requisições com corpo devem enviar o cabeçalho `Content-Type: application/json`.

---

## 10. Exemplos de requisições

### Criar categoria — `POST /categories`

```json
{
  "name": "Pizzas",
  "description": "Pizzas artesanais com diversos sabores e ingredientes.",
  "icon": "🍕",
  "display_order": 1,
  "active": true
}
```

### Atualizar categoria — `PUT /categories/:id`

```json
{
  "name": "Pizzas Especiais",
  "description": "Pizzas tradicionais e especiais"
}
```

### Criar produto — `POST /products`

O `category_id` deve ser o UUID de uma categoria existente.

```json
{
  "category_id": "9913e898-7302-4d20-80ac-2c0e703449cf",
  "title": "Pizza de Chocolate",
  "description": "Pizza doce com chocolate ao leite e granulado.",
  "price": 35.9,
  "available": true
}
```

### Atualizar produto — `PUT /products/:id`

```json
{
  "category_id": "9913e898-7302-4d20-80ac-2c0e703449cf",
  "title": "Pizza de Chocolate - Grande",
  "description": "Pizza doce com chocolate ao leite e granulado, tamanho grande.",
  "price": 42.5,
  "available": true
}
```

### Exemplo com cURL

```bash
curl -X POST http://localhost:3000/categories \
  -H "Content-Type: application/json" \
  -d '{"name":"Pizzas","description":"Pizzas tradicionais e especiais","display_order":1}'
```

### Coleção do Postman

O arquivo `restaurant-ordering-system-API.postman_collection.json` contém todas as requisições acima prontas para importar no Postman (**Import → File**).

---

## Licença

Distribuído sob a licença **ISC**.
