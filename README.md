# Sistema de Gestão de Produtos e Pedidos

Backend desenvolvido em Node.js para a disciplina Conexão de Banco de Dados com POO.
O sistema implementa o fluxo de um delivery de restaurante, com foco principal no uso de triggers, functions e procedures no PostgreSQL para garantir integridade, automação de regras de negócio e processamento de pedidos.

## Tecnologias

- Node.js (ES Modules)
- Express.js
- PostgreSQL
- Docker & Docker Compose
- pg (PostgreSQL client)

## Estrutura do Projeto

```
src/
├── config/
│   └── database.js          # Configuração do pool de conexões
├── database/
│   └── migrations/          # Scripts SQL (tabelas, funções, triggers)
├── modules/
│   ├── produtos/            # Módulo de produtos
│   │   ├── produto.controller.js
│   │   ├── produto.service.js
│   │   └── produto.repository.js
│   └── pedidos/             # Módulo de pedidos
│       ├── pedido.controller.js
│       ├── pedido.service.js
│       └── pedido.repository.js
├── errors/
│   └── AppError.js          # Classes de erro customizadas
└── server.js                 # Servidor Express
```

## Docker

### Iniciar os serviços

```bash
docker-compose up -d
```

### Parar os serviços

```bash
docker-compose down
```

## Banco de Dados

### Executar migrações

As migrações são executadas automaticamente ao iniciar o container do PostgreSQL. Para executar manualmente:

```bash
npm run migrate
```

### Conectar ao PGAdmin

- URL: http://localhost:5050
- Email: admin@admin.com
- Senha: admin

## Endpoints

### Produtos

- `GET /produtos` - Listar todos os produtos
- `GET /produtos/:id` - Buscar produto por ID
- `POST /produtos` - Criar novo produto
- `PUT /produtos/:id` - Atualizar produto
- `DELETE /produtos/:id` - Deletar produto

**Exemplo POST /produtos:**

```json
{
    "categoria_id": 1,
    "nome": "Produto Exemplo",
    "descricao": "Descrição do produto",
    "preco": 29.99,
    "ativo": true
}
```

### Pedidos

- `GET /pedidos` - Listar todos os pedidos
- `GET /pedidos/:id` - Buscar pedido por ID
- `POST /pedidos` - Criar novo pedido
- `PUT /pedidos/:id` - Atualizar pedido
- `DELETE /pedidos/:id` - Deletar pedido

**Exemplo POST /pedidos:**

```json
{
    "itens": [
        {
            "produto_id": 1,
            "quantidade": 2
        },
        {
            "produto_id": 2,
            "quantidade": 1
        }
    ]
}
```

### Listar produtos

```bash
curl http://localhost:3000/produtos
```

### Listar pedidos

```bash
curl http://localhost:3000/pedidos
```

## 📝 Regras de Negócio

### Produtos

- Nome obrigatório e único
- Preço deve ser maior que zero
- Descrição opcional
- Não pode deletar produto em uso em pedidos

### Pedidos

- Deve conter ao menos um item
- Quantidade deve ser maior que zero
- Valor total calculado automaticamente via triggers
- Subtotal calculado automaticamente (quantidade × preço)

## Variáveis de Ambiente

Crie um arquivo `.env` na raiz do projeto:

```env
DB_HOST=localhost
DB_PORT=5432
DB_USER=postgres
DB_PASSWORD=postgres
DB_NAME=projeto
PORT=3000
```

## Scripts

- `npm start` - Inicia o servidor
- `npm run dev` - Inicia o servidor em modo watch
- `npm run migrate` - Executa as migrações do banco

## Arquitetura

O projeto segue arquitetura em camadas:

1. **Controller**: Recebe requisições HTTP e retorna respostas
2. **Service**: Aplica regras de negócio e validações
3. **Repository**: Acessa o banco de dados via pg

## Tratamento de Erros

O sistema utiliza classes de erro customizadas:

- `AppError` - Erro base
- `ValidationError` - Erros de validação (400)
- `NotFoundError` - Recurso não encontrado (404)
- `ConflictError` - Conflito (409)
