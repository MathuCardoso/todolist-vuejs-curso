# Roteiro de Aula Completo — Lista de Tarefas com Vue 3

**Disciplina:** Projeto de Extensão — TADS, 2º período
**Horário:** 19:00 às 22:00 (intervalo 20:30–20:50) — 2h40 de aula efetiva
**Produto final:** SPA "Cadastro de A Fazeres" com cadastro, edição, exclusão e validação (Vue 3 + Vite + Vue Router)

Este roteiro traz **todos os passos e todo o código**, na ordem em que será digitado. Cada etapa termina com um **ponto de controle** para conferir se a turma está acompanhando.

---

## Cronograma

| Horário | Duração | Etapa |
|---|---|---|
| 19:00 – 19:15 | 15 min | 1. Abertura e ambiente |
| 19:15 – 19:35 | 20 min | 2. Conceitos essenciais |
| 19:35 – 19:55 | 20 min | 3. Criando o projeto |
| 19:55 – 20:10 | 15 min | 4. Layout, rotas e estilo |
| 20:10 – 20:30 | 20 min | 5. Primeira tela e reatividade |
| **20:30 – 20:50** | **20 min** | **Intervalo** |
| 20:50 – 21:10 | 20 min | 6. Componentes `Input` e `Button` |
| 21:10 – 21:30 | 20 min | 7. Cadastro com validação |
| 21:30 – 21:50 | 20 min | 8. Listagem, edição e exclusão |
| 21:50 – 22:00 | 10 min | 9. Fechamento |

> **Se o tempo apertar:** a etapa 8 pode ter a edição entregue como desafio. Priorize: componentes → cadastro com validação → listagem.

---

## 1. Abertura e ambiente (19:00 – 19:15)

**Fala sugerida:** apresentar o mini curso, mostrar o resultado final funcionando (cadastrar, editar, excluir) e explicar que ao final da noite cada aluno terá o mesmo projeto rodando.

**Checklist do ambiente** (resolver pendências agora):

```bash
node -v    # precisa ser ^22.18.0 ou >=24.12.0
npm -v
```

- VS Code instalado com a extensão **Vue (Official)**
- Navegador com DevTools (F12)

---

## 2. Conceitos essenciais (19:15 – 19:35)

Explicação curta, o conceito volta na prática.

| Conceito | Em uma frase |
|---|---|
| **SPA** | Uma única página HTML; o JavaScript troca o conteúdo sem recarregar. |
| **Vue** | Framework que atualiza a tela automaticamente quando os dados mudam. |
| **Vite** | Ferramenta que roda o projeto em desenvolvimento e gera o build final. |
| **Componente** | Arquivo `.vue` com `<script>`, `<template>` e `<style>`, reutilizável. |
| **`ref`** | Cria um valor reativo; no script usa-se `.value`, no template não. |
| **Diretivas** | `v-model` (liga campo ↔ dado), `v-for` (repetir), `v-if` (condicional), `@click` (evento). |

Estrutura de um arquivo `.vue`:

```vue
<script setup>
  // lógica
</script>

<template>
  <!-- HTML -->
</template>

<style scoped>
  /* CSS só deste componente */
</style>
```

---

## 3. Criando o projeto (19:35 – 19:55)

### 3.1 Gerar o projeto

```bash
npm create vue@latest todo-projeto
```

Respostas do assistente:

| Pergunta | Resposta |
|---|---|
| Project name | `todo-projeto` |
| Features | marque **somente** `Router (SPA development)` (e `Vue DevTools`, se aparecer, é opcional) |
| Iniciar projeto em branco, sem código de exemplo | **marcar** (`Yes`) |
| Demais perguntas (experimentais etc.) | `No` |

> As perguntas do assistente variam um pouco entre versões. Ao escolher o projeto em branco, o assistente já não gera as telas, os componentes e os estilos de exemplo.

### 3.2 Instalar e rodar

```bash
cd todo-projeto
npm install
npm run dev
```

Abrir o endereço exibido (normalmente `http://localhost:5173`).

### 3.3 Tour pela estrutura

```
todo-projeto/
├── index.html          # página única da aplicação
├── vite.config.js      # configuração do Vite (inclui o alias @)
├── package.json        # dependências e scripts
└── src/
    ├── main.js         # ponto de entrada
    ├── App.vue         # componente raiz
    └── router/         # rotas
```

Como o projeto foi criado em branco, não há telas, componentes nem estilos de exemplo para apagar. Vamos criar tudo do zero.

### 3.4 Criar as pastas do projeto

Criar as pastas que usaremos:

```bash
mkdir -p src/pages src/layouts src/components/icons
```

### 3.5 `src/main.js`

Conferir se o conteúdo é este (se houver algum import de CSS, remova):

```js
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'

const app = createApp(App)

app.use(router)

app.mount('#app')
```

### 3.6 Conferir o alias `@`

O alias `@` aponta para a pasta `src/` e evita caminhos como `../../components`. Conferir se o `vite.config.js` contém:

```js
import { fileURLToPath, URL } from 'node:url'

import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import vueDevTools from 'vite-plugin-vue-devtools'

// https://vite.dev/config/
export default defineConfig({
  plugins: [
    vue(),
    vueDevTools(),
  ],
  resolve: {
    alias: {
      '@': fileURLToPath(new URL('./src', import.meta.url)),
    },
  },
})
```

> Se a opção Vue DevTools não foi marcada, remova o import e a linha `vueDevTools(),`.

E o `jsconfig.json` (faz o VS Code entender o alias):

```json
{
  "compilerOptions": {
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "exclude": ["node_modules", "dist"]
}
```

> ✅ **Ponto de controle:** o projeto roda (`npm run dev`) e a página aparece em branco, sem erros no console.

---

## 4. Layout, rotas e estilo (19:55 – 20:10)

### 4.1 `index.html` (adicionar Bootstrap via CDN)

Substituir todo o conteúdo:

```html
<!doctype html>
<html lang="pt-BR">
    <head>
        <meta charset="UTF-8" />
        <link rel="icon" href="/favicon.ico" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>TodoList</title>
        <link
            href="https://cdn.jsdelivr.net/npm/bootstrap@6.0.0-alpha.1/dist/css/bootstrap.min.css"
            rel="stylesheet"
            integrity="sha384-B/GM4XqrwHnWXNOWMbloTmrYXZg10cakYGmpfsR/bbzQ6JAJI4ihuyADKLnBgrCe"
            crossorigin="anonymous"
        />
    </head>
    <body>
        <div id="app"></div>
        <script type="module" src="/src/main.js"></script>
        <script
            type="module"
            src="https://cdn.jsdelivr.net/npm/bootstrap@6.0.0-alpha.1/dist/js/bootstrap.bundle.min.js"
            integrity="sha384-1a/pXj49ZQ1aHEmrJ+gMw1otqoVsYwlEnlD8mIfY2TV03r20Y0CN7uqx1tQogjPL"
            crossorigin="anonymous"
        ></script>
    </body>
</html>
```

> Este projeto usa o Bootstrap 6 (alpha) via CDN, por isso classes como `gap-10`, `p-10`, `btn-solid` e `theme-success` existem. Os alunos precisam de internet para o estilo carregar.

### 4.2 `src/layouts/MainLayout.vue`

Um layout que envolve as páginas. O `<slot>` marca onde o conteúdo da página será colocado.

```vue
<script setup></script>

<template>
    <div class="p-10">
        <slot></slot>
    </div>
</template>
```

### 4.3 `src/App.vue`

O `<RouterView />` mostra a página da rota atual, dentro do layout.

```vue
<script setup>
    import MainLayout from "./layouts/MainLayout.vue";
</script>

<template>
    <MainLayout>
        <RouterView />
    </MainLayout>
</template>

<style scoped></style>
```

### 4.4 `src/pages/TodoList.vue` (versão inicial, só para a rota funcionar)

```vue
<script setup></script>

<template>
    <h1 class="text-center fw-bold">Cadastro de A Fazeres</h1>
</template>
```

### 4.5 `src/router/routes.js`

Separamos as rotas em um arquivo próprio:

```js
import TodoList from "@/pages/TodoList.vue";

export const routes = [
    {
        path: "/",
        component: TodoList,
    },
];
```

### 4.6 `src/router/index.js`

```js
import { createRouter, createWebHistory } from 'vue-router'
import { routes } from './routes'

const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes: routes,
})

export default router
```

> ✅ **Ponto de controle:** o título **"Cadastro de A Fazeres"** aparece centralizado, sem erros no console.

---

## 5. Primeira tela e reatividade (20:10 – 20:30)

Objetivo: montar o formulário com `<input>` simples e ver a reatividade funcionando.

### 5.1 `src/pages/TodoList.vue`

```vue
<script setup>
    import { ref } from "vue";

    const todo = ref({
        id: null,
        title: "",
        description: "",
        created_at: null,
    });

    const todos = ref([]);
</script>

<template>
    <div class="d-flex flex-column gap-10">
        <h1 class="text-center fw-bold">Cadastro de A Fazeres</h1>

        <div class="w-100 d-flex justify-content-center align-items-center">
            <div class="w-25 d-flex flex-column gap-6">
                <input
                    class="form-control"
                    placeholder="Título da tarefa"
                    v-model="todo.title"
                />
                <input
                    class="form-control"
                    placeholder="Descrição"
                    v-model="todo.description"
                />

                <!-- Demonstração da reatividade (será removida depois) -->
                <p>Título: {{ todo.title }}</p>
                <p>Descrição: {{ todo.description }}</p>
            </div>
        </div>

        <div>
            <h2 class="text-sm m-0">
                A fazeres cadastrados: {{ todos.length }}
            </h2>
        </div>
    </div>
</template>

<style scoped></style>
```

**Pontos para explicar:**

- `ref({...})` cria um objeto reativo; no `<script>` acessamos com `todo.value`, no `<template>` apenas `todo`.
- `v-model` liga o campo ao dado nos dois sentidos: digitou, o dado muda; o dado mudou, o campo muda.
- `{{ }}` mostra o valor e atualiza sozinho. **Este é o momento "uau" da aula** — deixe os alunos digitarem e verem o texto mudar.

> ✅ **Ponto de controle:** digitar nos campos atualiza os textos "Título:" e "Descrição:" em tempo real.

---

## ☕ Intervalo (20:30 – 20:50)

---

## 6. Componentes `Input` e `Button` (20:50 – 21:10)

**Por que componentes?** Evitam repetir HTML e padronizam a aparência. Vamos criar dois, e depois substituir os elementos soltos.

### 6.1 `src/components/Button.vue`

```vue
<script setup>
    const props = defineProps({
        type: {
            type: String,
            default: "button",
        },
        addClass: String,
    });

    const emit = defineEmits(["click"]);
</script>

<template>
    <div class="w-100">
        <button
            @click="emit('click')"
            :type="props.type"
            class="btn-solid"
            :class="props.addClass"
        >
            <slot></slot>
        </button>
    </div>
</template>

<style scoped></style>
```

**Explicar:**

- `defineProps`: o que o componente **recebe** do pai (`type`, `addClass`).
- `defineEmits`: o que o componente **avisa** ao pai (`click`).
- `<slot>`: o texto/conteúdo que o pai colocar entre `<Button>` e `</Button>`.

### 6.2 `src/components/Input.vue`

```vue
<script setup>
    const props = defineProps({
        type: {
            type: String,
            default: "text",
        },
        placeholder: String,
        label: String,
        id: String,
        error: String,
        modelValue: String,
    });
    const emit = defineEmits(["update:modelValue"]);
</script>

<template>
    <div class="form-field">
        <label v-if="props.label" :for="props.id" class="form-label">
            {{ props.label }}
        </label>
        <input
            :type="props.type"
            :placeholder="props.placeholder"
            :id="props.id"
            class="form-control"
            :value="props.modelValue"
            @input="emit('update:modelValue', $event.target.value)"
        />
        <span v-if="props.error" class="fg-danger fw-bold">{{ props.error }}</span>
    </div>
</template>

<style scoped></style>
```

**Explicar o `v-model` customizado:** `v-model` num componente é um atalho para:

```vue
<Input :model-value="todo.title" @update:model-value="todo.title = $event" />
```

Ou seja, o componente **recebe** `modelValue` e **emite** `update:modelValue` quando o usuário digita. É o par `props` + `emit` funcionando junto.

### 6.3 Atualizar `src/pages/TodoList.vue`

Adicionar os imports no `<script setup>`:

```js
import Button from "@/components/Button.vue";
import Input from "@/components/Input.vue";
```

Trocar o bloco do formulário no `<template>` (remove os `<input>` e os `<p>` de demonstração):

```vue
<div class="w-100 d-flex justify-content-center align-items-center">
    <div class="w-25 d-flex flex-column gap-6">
        <Input label="Título da tarefa" v-model="todo.title" />
        <Input label="Descrição" v-model="todo.description" />
        <Button add-class="theme-success w-100">Salvar</Button>
    </div>
</div>
```

> ✅ **Ponto de controle:** a tela tem dois campos com rótulo e um botão "Salvar" (que ainda não faz nada).

---

## 7. Cadastro com validação (21:10 – 21:30)

### 7.1 Atualizar o `<script setup>` de `src/pages/TodoList.vue`

```vue
<script setup>
    import Button from "@/components/Button.vue";
    import Input from "@/components/Input.vue";
    import { ref } from "vue";

    const todo = ref({
        id: null,
        title: "",
        description: "",
        created_at: null,
    });

    const todos = ref([]);

    const errors = ref({});

    function saveTodo() {
        if (!validateData()) return;

        const todosLen = todos.value.length;
        let newId = 1;

        if (todosLen != 0) {
            newId = todos.value[todosLen - 1].id + 1;
        }

        todo.value.id = newId;
        todo.value.created_at = new Date();

        todos.value.push(todo.value);
        clearInputs();
        alert("Adicionado com sucesso!");
    }

    function validateData() {
        let hasErrors = false;

        Object.entries(todo.value).forEach(([campo, value]) => {
            if (campo == "id" || campo == "created_at") return;

            if (!value) {
                errors.value[campo] = `O campo [${campo}] não pode ser vazio.`;
                hasErrors = true;
                return;
            }
            errors.value[campo] = null;
        });

        return !hasErrors;
    }

    function clearInputs() {
        todo.value = {
            id: null,
            title: "",
            description: "",
            created_at: null,
        };
    }
</script>
```

### 7.2 Atualizar o formulário no `<template>`

```vue
<div class="w-100 d-flex justify-content-center align-items-center">
    <div class="w-25 d-flex flex-column gap-6">
        <Input
            label="Título da tarefa"
            v-model="todo.title"
            :error="errors.title"
            @update:model-value="errors.title = null"
        />
        <Input
            label="Descrição"
            v-model="todo.description"
            :error="errors.description"
            @update:model-value="errors.description = null"
        />
        <Button add-class="theme-success w-100" @click="saveTodo">
            Salvar
        </Button>
    </div>
</div>
```

**Explicar:**

- `saveTodo`: valida → gera o `id` (último id + 1) → define `created_at` → `push` no array → limpa o formulário.
- `validateData`: percorre os campos (ignorando `id` e `created_at`); se vazio, guarda a mensagem em `errors`; retorna `true` só se não houve erro.
- `:error="errors.title"` passa a mensagem para o `Input`, que a exibe com `v-if`.
- `@update:model-value="errors.title = null"` apaga o erro assim que o usuário volta a digitar.
- Conferir o contador **"A fazeres cadastrados"** subindo a cada cadastro (as tarefas ainda não aparecem na tela).

> ✅ **Ponto de controle:** campo vazio mostra mensagem em vermelho; campos preenchidos exibem o alerta de sucesso e o contador aumenta.

---

## 8. Listagem, edição e exclusão (21:30 – 21:50)

### 8.1 Ícones SVG como componentes

`src/components/icons/Pencil.vue`:

```vue
<script setup></script>

<template>
    <svg
        xmlns="http://www.w3.org/2000/svg"
        width="20"
        height="20"
        viewBox="0 0 24 24"
        fill="none"
        stroke="currentColor"
        stroke-width="2"
        stroke-linecap="round"
        stroke-linejoin="round"
        class="lucide lucide-square-pen preview-icon"
    >
        <path d="M12 3H5a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7" />
        <path
            d="M18.375 2.625a1 1 0 0 1 3 3l-9.013 9.014a2 2 0 0 1-.853.505l-2.873.84a.5.5 0 0 1-.62-.62l.84-2.873a2 2 0 0 1 .506-.852z"
        />
    </svg>
</template>
```

`src/components/icons/Trash.vue`:

```vue
<script setup></script>

<template>
    <svg
        xmlns="http://www.w3.org/2000/svg"
        width="20"
        height="20"
        viewBox="0 0 24 24"
        fill="none"
        stroke="currentColor"
        stroke-width="2"
        stroke-linecap="round"
        stroke-linejoin="round"
        class="lucide lucide-trash preview-icon"
    >
        <path d="M10 11v6" />
        <path d="M14 11v6" />
        <path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6" />
        <path d="M3 6h18" />
        <path d="M8 6V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2" />
    </svg>
</template>
```

> Os ícones vêm do [Lucide](https://lucide.dev/). Basta copiar o SVG e colocá-lo dentro de um `<template>`.

### 8.2 `src/pages/TodoList.vue` — versão final completa

```vue
<script setup>
    import Button from "@/components/Button.vue";
    import Pencil from "@/components/icons/Pencil.vue";
    import Trash from "@/components/icons/Trash.vue";
    import Input from "@/components/Input.vue";
    import { ref } from "vue";

    const todo = ref({
        id: null,
        title: "",
        description: "",
        created_at: null,
    });

    const todos = ref([]);

    const errors = ref({});

    function saveTodo(index) {
        if (!validateData()) return;

        // Edição: já existe id, então substitui o item na posição original
        if (todo.value.id) {
            todos.value[index] = todo.value;
            clearInputs();
            alert("Alterado com sucesso!");
            return;
        }

        // Cadastro: gera novo id e data de criação
        const todosLen = todos.value.length;
        let newId = 1;

        if (todosLen != 0) {
            newId = todos.value[todosLen - 1].id + 1;
        }

        todo.value.id = newId;
        todo.value.created_at = new Date();

        todos.value.push(todo.value);
        clearInputs();
        alert("Adicionado com sucesso!");
    }

    function deleteTodo(id) {
        const index = todos.value.findIndex((todo) => todo.id == id);

        if (index == -1) {
            alert("Tarefa não encontrada.");
            return;
        }

        if (confirm("Tem certeza que deseja deletar este a fazer?")) {
            todos.value.splice(index, 1);
        }
    }

    function updateTodo(id) {
        const todoUpdate = findToDo(id, true);
        if (!todoUpdate[0]) return;

        // Spread cria uma CÓPIA: o card só muda ao clicar em "Salvar alteração"
        todo.value = { ...todoUpdate[0] };
    }

    function validateData() {
        let hasErrors = false;

        Object.entries(todo.value).forEach(([campo, value]) => {
            if (campo == "id" || campo == "created_at") return;

            if (!value) {
                errors.value[campo] = `O campo [${campo}] não pode ser vazio.`;
                hasErrors = true;
                return;
            }
            errors.value[campo] = null;
        });

        return !hasErrors;
    }

    function findToDo(id, returnIndex) {
        const index = todos.value.findIndex((todo) => todo.id == id);

        if (index == -1) {
            return [];
        }

        return returnIndex ? [todos.value[index], index] : todos.value[index];
    }

    function clearInputs() {
        todo.value = {
            id: null,
            title: "",
            description: "",
            created_at: null,
        };
    }
</script>

<template>
    <div class="d-flex flex-column gap-10">
        <h1 class="text-center fw-bold">Cadastro de A Fazeres</h1>

        <div class="w-100 d-flex justify-content-center align-items-center">
            <div class="w-25 d-flex flex-column gap-6">
                <Input
                    label="Título da tarefa"
                    v-model="todo.title"
                    :error="errors.title"
                    @update:model-value="errors.title = null"
                />
                <Input
                    label="Descrição"
                    v-model="todo.description"
                    :error="errors.description"
                    @update:model-value="errors.description = null"
                />
                <Button
                    add-class="theme-success w-100"
                    @click="
                        todo.id
                            ? saveTodo(findToDo(todo.id, true)[1])
                            : saveTodo()
                    "
                >
                    {{ todo.id ? "Salvar alteração" : "Salvar" }}
                </Button>
            </div>
        </div>

        <div>
            <h2 class="text-sm m-0">
                A fazeres cadastrados: {{ todos.length }}
            </h2>
        </div>

        <div class="d-flex flex-wrap gap-10">
            <section
                class="card"
                style="max-width: 320px"
                v-for="todo in todos"
                :key="todo.id"
            >
                <div class="card-body">
                    <h4 class="card-title fw-bold">
                        {{ todo.title.toUpperCase() }}
                    </h4>
                    <p class="card-text">
                        {{ todo.description }}
                    </p>
                    <span>{{
                        todo.created_at.toLocaleString("pt-BR", {
                            day: "2-digit",
                            month: "2-digit",
                            year: "numeric",
                            hour: "2-digit",
                            minute: "2-digit",
                        })
                    }}</span>
                    <div class="d-flex gap-6">
                        <Button
                            add-class="bg-info w-100"
                            @click="updateTodo(todo.id)"
                            >Editar <Pencil />
                        </Button>
                        <Button
                            add-class="bg-danger w-100"
                            @click="deleteTodo(todo.id)"
                            >Excluir <Trash
                        /></Button>
                    </div>
                </div>
            </section>
        </div>
    </div>
</template>

<style scoped></style>
```

### 8.3 Passo a passo para explicar (nesta ordem)

1. **Listar:** `v-for="todo in todos" :key="todo.id"` repete o card para cada item; `:key` ajuda o Vue a identificar cada item.
2. **Data:** `toLocaleString("pt-BR", {...})` formata a `Date` como `dd/mm/aaaa hh:mm`.
3. **Excluir:** `findIndex` localiza a posição → `confirm` pede confirmação → `splice(index, 1)` remove. O `return` após o alerta impede de continuar quando a tarefa não existe.
4. **Editar (parte 1):** `updateTodo` copia a tarefa para o formulário. O botão passa a mostrar **"Salvar alteração"** porque `todo.id` agora existe.
5. **Editar (parte 2):** `saveTodo(index)` vê que há `id` e substitui o item da lista naquela posição, em vez de adicionar um novo.

### 8.4 Demonstração sobre referência e cópia (5 min, vale a pena)

Para mostrar *por que* usamos o spread em `updateTodo`:

1. Trocar temporariamente a linha por `todo.value = todoUpdate[0];` (sem o spread).
2. Clicar em **Editar** e digitar no campo: o **card muda enquanto digita**, sem salvar.
3. Explicar: sem o spread, o formulário e o card apontam para o **mesmo objeto** na memória.
4. Voltar para `todo.value = { ...todoUpdate[0] };`: agora o formulário trabalha numa **cópia**, e o card só muda ao salvar.

> ✅ **Ponto de controle:** cadastrar, editar e excluir funcionam; editar não altera o card antes de salvar.

---

## 9. Fechamento (21:50 – 22:00)

### 9.1 Gerar o build

```bash
npm run build
npm run preview
```

Mostrar que o projeto está pronto para publicação (pasta `dist/`).

### 9.2 O que foi aprendido

- Criar um projeto Vue com Vite e Vue Router
- Componentes, `props`, `emit` e `slot`
- Reatividade com `ref` e `v-model`
- Renderização de listas com `v-for`
- Validação de formulário
- Diferença entre referência e cópia de objetos

### 9.3 Limitações e próximos passos

| Limitação atual | Melhoria possível |
|---|---|
| Os dados somem ao recarregar a página | Salvar no `localStorage` ou numa API |
| `alert` e `confirm` do navegador | Componentes de aviso e de confirmação |
| Não há como concluir uma tarefa | Campo `done` com checkbox |
| Sem busca ou filtros | Campo de busca com `computed` |
| Id gerado a partir do último item | Usar `crypto.randomUUID()` |

### 9.4 Feedback

Pedir à turma: o que foi mais fácil, o que foi mais difícil e o que gostariam de ver numa próxima aula.

---

## Apêndice: problemas comuns

| Sintoma | Causa provável | Solução |
|---|---|---|
| Página sem estilo | Sem internet (Bootstrap via CDN) | Conferir conexão ou usar a rede da sala |
| `Failed to resolve import "@/..."` | Alias `@` não configurado | Conferir `vite.config.js` e reiniciar o `npm run dev` |
| Tela em branco | Erro no console | Abrir F12 → Console e ler a mensagem |
| `Cannot read properties of null (reading 'toLocaleString')` | Tarefa sem `created_at` | Conferir se o `saveTodo` define `created_at = new Date()` |
| Botão não reage ao clique | Falta `@click` ou `emit('click')` no `Button.vue` | Conferir `defineEmits` e o `@click` no botão do componente |
| Campo não atualiza ao digitar | `v-model` ligado errado ou `Input` sem `emit` | Conferir `update:modelValue` no `Input.vue` |
| `npm` não reconhecido | Node não instalado | Reinstalar o Node.js e reabrir o terminal |

## Dicas para condução

- Programe **ao vivo**; evite colar blocos grandes sem explicar.
- Use os **pontos de controle** antes de avançar; quem ficar para trás pode copiar o código da etapa anterior deste roteiro.
- Peça que alunos mais ágeis ajudem os colegas ao lado.
- Deixe o projeto final pronto num repositório para quem precisar se atualizar.
