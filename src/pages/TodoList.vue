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

        if (todo.value.id) {
            todos.value[index] = todo.value;
            clearInputs();
            alert("Alterado com sucesso!");
            return;
        }

        const todosLen = todos.value.length;
        let newId = 1;

        if (todosLen != 0) {
            newId = todos.value[todosLen - 1].id + 1;
        }

        todo.value.id = newId;
        todo.value.created_at = new Date();

        const newTodo = todo.value;

        todos.value.push(newTodo);
        clearInputs();
        alert("Adicionado com sucesso!");
    }

    function deleteTodo(id) {
        let index = todos.value.findIndex((todo) => todo.id == id);

        if (index == -1) {
            alert("Todo não encontrada.");
        }

        if (confirm("Tem certeza que deseja deletar esta tarefa?")) {
            todos.value.splice(index, 1);
        }
    }

    function updateTodo(id) {
        const todoUpdate = findToDo(id, true);
        if (!todoUpdate[0]) return;
        todo.value = {...todoUpdate[0]};
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

        return hasErrors ? false : true;
    }

    function findToDo(id, returnIndex) {
        let index = todos.value.findIndex((todo) => todo.id == id);

        if (index == -1) {
            return {};
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
        <h1 class="text-center fw-bold">Cadastro de Tarefas</h1>
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
                Trefas cadastrados: {{ todos.length }}
            </h2>
        </div>
        <div class="grid grid-cols-4">
            <section
                class="card h-fit"
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
