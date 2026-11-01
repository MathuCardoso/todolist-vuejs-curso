# Todo Projeto

Aplicação simples de lista de tarefas ("A Fazeres") feita com Vue 3 e Vite. Permite cadastrar, editar e excluir tarefas, com validação dos campos.

## Funcionalidades

- Cadastrar tarefa com título e descrição
- Editar tarefas existentes
- Excluir tarefas (com confirmação)
- Validação de campos obrigatórios
- Exibição da data e hora de criação

> Os dados ficam apenas em memória: ao recarregar a página, as tarefas são perdidas.

## Tecnologias

- [Vue 3](https://vuejs.org/) (Composition API, `<script setup>`)
- [Vue Router](https://router.vuejs.org/)
- [Vite](https://vite.dev/)
- [Bootstrap](https://getbootstrap.com/) (via CDN)

## Pré-requisitos

- Node.js `^22.18.0` ou `>=24.12.0`

## Como executar

```bash
# instalar dependências
npm install

# iniciar o servidor de desenvolvimento
npm run dev

# gerar build de produção
npm run build

# visualizar o build localmente
npm run preview
```

## Estrutura do projeto

```
src/
├── components/   # Componentes reutilizáveis (Button, Input, ícones)
├── layouts/      # Layouts da aplicação
├── pages/        # Páginas (TodoList)
├── router/       # Configuração de rotas
├── App.vue
└── main.js
```