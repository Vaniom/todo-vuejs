<script setup>

import { ref, computed, watchEffect } from 'vue'

const text = ref('')

// Clé de stockage dans localStorage
const STORAGE_KEY = 'todoList-vue'

// Lecture des données dans localStorage. Si aucune donnée, on definit un tableau vide
const todos = ref(JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]'))



const displayList = ref('all')

const filteredTodos = computed(() => {
    if (displayList.value == 'all') {
        return todos.value
    } else if (displayList.value == 'completed') {
        return todos.value.filter((t) => t.done)
    }else if (displayList.value == 'inProgress') {
        return todos.value.filter((t) => !t.done)
    }
})

// watchEffect fait tourner la fonction setItem chaque fois que les dépendences sont modifiées, ici: todos
watchEffect(() => {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(todos.value));
    //localStorage.setItem(ID, JSON.stringify(id.value))
})



function addTodo(text) {
    const newTodo = {
        id: new Date().getTime(), // definit un id unique chaque milliseconde
        text: text,
        done: false
    }
    todos.value.push(newTodo);
    this.text = ""; // vider rapidement le formulaire
}

function removeTodo(todo) {
    let array = filteredTodos.value;
    let index = array.indexOf(todo);
    array.splice(index, 1);
}

</script>

<template>
    <div class="wrapper">
        <h1>Todos</h1>
        <form name="todoField" id="todoField" class="searchArea" @submit.prevent="addTodo(text)">
            <input type="text" id="textField" class="textField" v-model="text" placeholder="Ajouter une tâche" required/>
            <button>+</button>
        </form>
        <div class="filterOptions">
            <input type="radio" id="all" value="all" name="filterTodos" v-model="displayList" checked />
            <label for ="all">Tout</label>
            <input type="radio" id="completed" value="completed" name="filterTodos" v-model="displayList" />
            <label for="completed">Terminés</label>
            <input type="radio" id="inProgress" value="inProgress" name="filterTodos" v-model="displayList" />
            <label for="inProgress">En cours</label>
        </div>
        <div class="todoContainer">
            <ul>
                <li v-for="todo of filteredTodos" :key="todo.id">
                        <input type="checkbox" class="checkDone" :id="todo.id" v-model="todo.done" />
                        <label :for="todo.id" :class="{ done: todo.done }">{{ todo.text }}</label>
                        <button class="removeTodo" @click="removeTodo(todo)">X</button>
                </li>
            </ul>

        </div>
        <p class="count">Nombre de tâches affichées : {{ filteredTodos.length }}</p>
    </div>
</template>

<style>

.wrapper {
    width: 500px;
    margin: 0 auto;
    padding: 10px 20px;
    background-color: lightgrey;
    font-size: 20px;
    font-family: Verdana, Geneva, Tahoma, sans-serif;
}

h1 {
    text-align: center;
    color: #8842d5;
    text-transform: lowercase;
    font-weight: 100;
    font-style: italic;
}

ul {
    padding-left: 10px;
}

li {
    list-style-type: none;
    padding: 5px 0;
    border-bottom: solid #8842d5 0.5px;
}

.count {
    font-size: 14px;
    font-style: italic;
}

.done {
    text-decoration: line-through;
}

.removeTodo {
    float: right;
    background-color: rgba(255, 255, 255, 0);
    border: none;
    color: red;
    font-size: 16px;
    font-weight: 900;
}

.todoContainer {
    width: 480px;
    margin-bottom: 30px;
}

.searchArea {
    margin-top: 30px;
    margin-bottom: 30px;
    vertical-align: baseline;
}

.searchArea button {
    float: right;
    padding: 6px 25px;
    color: white;
    font-size: 18px;
    background-color: #8842d5;
    border-radius: 5px;
}

.textField {
    width: 80%;
    border: 4px solid #8842d5;
    border-radius: 5px;
    padding: 5px 5px;
    font-size: 16px;
}

input[type="checkbox"] {
    margin-right: 10px
}

</style>