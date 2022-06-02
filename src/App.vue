<template>
  <div id="app">
    <!-- Menu Principal -->
    <nav id="header" class="navbar is-dark" role="navigation" aria-label="main navigation">
      <div class="navbar-brand">
        <!-- Logo de l'aplicació -->
        <a class="navbar-item" href="index.html">
          <img src="./assets/logo.png">
        </a>
        <a role="button" class="navbar-burger" aria-label="menu" aria-expanded="false" data-target="navbarBasicExample">
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
        </a>
      </div>
      <!-- Part del Menu que s'ocultara quan sigui un móbil -->
      <div id="navbarBasicExample" class="navbar-menu">
        <!-- Part del Menu que es situa a la dreta de la pantalla -->
        <div class="navbar-end">
          <div class="navbar-item">
            <div class="buttons">
              <!-- Input per afegir un Todo des del Menu -->
              <div class="field p-3">
                <input class="input is-dark" type="text" placeholder="Add Todo" v-model="newTodo" @change="addNewTodo()">
              </div>
            </div>
          </div>
        </div>
      </div>
    </nav>
    <!-- Cos de l'aplicació -->
    <div class="container is-max-desktop mt-6">
      <!-- Titol o text Principal -->
      <h1 class="title is-1 is-spaced has-text-centered mb-6 has-text-dark">Todos</h1>
      <form @submit.prevent="addNewTodo()" class="FormAddTodo">
        <!-- Camp del Formulari que afageix un Todo nou al clicar al boto "Add Todo" -->
        <div class="field">
          <input @change="addNewTodo()" class="input is-dark" v-model="newTodo" type="text" placeholder="Add Todo">
        </div>
        <div class="field">
          <button class="button is-link">Add Todo</button>
        </div>
        <!-- Boto que permet posar tots els Todos completats -->
        <div class="field">
          <a @click="markAllCompleted()" class="button is-link"><i class="fas fa-check-circle fa-lg icon mt-3"></i></a>
        </div>
      </form>
      <!-- div on es veuran tots els Todos creats -->
      <div id="todos" class="Todos mt-3">
        <!-- Per veure tots els Todos, "v-for" permet recorrer un array "todos" i per accedir a les dades i mostrar el text només cal posar "{{todo.text}}".
        I quan es faci clic a un Todo, s'executara la funció "todoCompleted(todo)" que canviara d'estat al Todo (completat o no completat).
        Per esborrar s'executara la funció "deleteTodo(todo)". -->
        <div id="todo" class="ItemTodo box is-flex is-justify-content-space-between is-clickable" 
        v-for="todo in todos" :key="todo.id" @click="todoCompleted(todo)" :class="{'has-background-danger': todo.isCompleted}">
          <h1 class="has-text-dark" :class="{'has-text-danger-dark completed': todo.isCompleted}">{{ todo.text }}</h1>
          <span class="icon deleteFree is-clickable" :class="{'deleteCompleted': todo.isCompleted}" @click="deleteTodo(todo)"><i class="fas fa-trash-alt"></i></span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  //Importa les funcions "ref" i "onMounted" de "vue"
  import { ref, onMounted } from "vue";

  export default {
    //Posem el nom del fitxer sense extensió ("App.vue" => "name: 'App'")
    name: 'App', 
    setup() {
      //I creem una constant "newTodo" i l'array "todos" on es guardaran tots els todo que creem
      const newTodo = ref('');
      const todos = ref([]);

      //I executa un event "onMounted", que quan s'executi l'aplicació en el navegador primer anira al localStorage i comprovara si existeix l'item "manTodos"
      onMounted(() => {
        if (localStorage.getItem('manTodos')) {
          //Si existeix, recuperar els Todos del localStorage
          let manTodos = JSON.parse(localStorage.getItem('manTodos'));
          manTodos.forEach(todo => {
            todos.value.push(todo);
          });
        }
      });

      //I ara crearem totes les funcions necessaries

      /*addNewTodo: Crea un Todo nou*/
      function addNewTodo() {
        //Comprova si la constant "newTodo" està definida
        if (newTodo.value.trim()) {
          //I es compleix, afageix un Todo nou amb un id, un text (especificat per l'usuari) i si està completat (per defecte "false")
          todos.value.push({
            id: Date.now(), 
            text: newTodo.value.trim(), 
            isCompleted: false
          });

          //I reinicia la constant "newTodo", i actualitza el localStorage
          newTodo.value = "";
          localStorage.setItem('manTodos', JSON.stringify(todos.value));
        }
      }

      /*todoCompleted: Canvia d'estat al Todo especificat en el parametre "completat o no"*/
      function todoCompleted(todo) {
        //Canvia d'estat al Todo especificat en el parametre "completat o no"
        todo.isCompleted = !todo.isCompleted;
        //I actualitza el localStorage
        localStorage.setItem('manTodos', JSON.stringify(todos.value));
      }

      /*deleteTodo: esborra al Todo especificat en el parametre*/
      function deleteTodo(todo) {
        //esborra al Todo especificat en el parametre
        todos.value = todos.value.filter( (item) => {
          return item != todo;
        });
        //I actualitza el localStorage
        localStorage.setItem('manTodos', JSON.stringify(todos.value));
      }

      /*markAllCompleted: marca tots els Todos com a completats*/
      function markAllCompleted() {
        //Marca tots els Todos com a completats
        todos.value.forEach(todo => {
          todo.isCompleted = true;
        });
        //I actualitza el localStorage
        localStorage.setItem('manTodos', JSON.stringify(todos.value));
      }

      //I finalment en el "return" afegirem les funcions i les variables creades anteriorment
      return {
        newTodo, 
        todos,  
        addNewTodo, 
        todoCompleted, 
        deleteTodo, 
        markAllCompleted
      }
    }
  }
</script>

<style>
  /* Dos mediaquerys perque el formulari per afegir Todos sigui responsive */
  @media screen and (min-width: 701px) {
    .FormAddTodo {
        display: flex;
        flex-direction: row;
        justify-content: center;
    }
    .FormAddTodo button {
      margin-right: 10px;
      margin-left: 10px;
    }
  }
  @media screen and (max-width: 700px) {
      .FormAddTodo {
          display: flex;
          flex-direction: column;
          justify-content: center;
          margin-right: 10%;
          margin-left: 10%;
      }
      .FormAddTodo button, .FormAddTodo a {
        width: 100%;
      }
  }
  * {
    margin: 0;
    padding: 0;
    font-family: Arial;
  }
  /* Estableix el fons de la pàgina */
  html, body {
    background-color: hsl(0, 0%, 96%);
  }
  /* Estils per els items (Todos) */
  .ItemTodo {
    background-color: hsl(219, 70%, 96%);
    transition: background-color 0.5s, transform 0.5s;
  }
  .ItemTodo:hover {
    transform: scale(1.03);
    background-color: hsl(219, 70%, 86%);
    transition: background-color 0.5s, transform 0.5s;
  }
  /* Estils per la icona de esborrar Todos (quan el Todo no està completat) */
  .deleteFree {
    color: hsl(348, 100%, 61%);
    transition: color 0.5s, transform 0.5s;
  }
  .deleteFree:hover {
    transform: scale(1.05);
    color: hsl(348, 86%, 43%);
    transition: color 0.5s, transform 0.5s;
  }
  /* Estils per la icona de esborrar Todos (quan el Todo està completat) */
  .deleteCompleted {
    color: hsl(348, 86%, 43%);
    transition: color 0.5s, transform 0.5s;
  }
  .deleteCompleted:hover {
    transform: scale(1.05);
    color: hsl(348, 86%, 33%);
    transition: color 0.5s, transform 0.5s;
  }
  /* Estils per que quan un Todo estigui completat sorti el text del Todo amb una linia al mig */
  .completed {
    text-decoration: line-through;
  }
</style>
