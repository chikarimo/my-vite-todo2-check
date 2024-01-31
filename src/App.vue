<script setup>
import TodoApp from "./components/TodoApp.vue";
import TodoListView from "./components/TodoListView.vue";
</script>

<template>
  <div class="wrap">
    <header><TodoApp></TodoApp></header>
    <main><TodoListView></TodoListView></main>
  </div>
</template>

<style scoped>
.wrap {
    font-size: 1rem;
    font-family:sans-serif;
    width: 800px;
    height: 600px;
    display: grid;
    grid-template:
        "... ...... ..." 20px
        "... header ..." 150px
        "... ...... ..." 20px
        "... main   ..." auto
        "... ...... ..." auto
        /20px auto  20px
        ;
}
header {
    grid-area: header;
    border-radius:5%;

}
main {
    grid-area: main;
    align-self:start;
}



@media screen and (max-width: 1000px){
  .wrap{
    grid-template:
    "header" 150px
    "main" 450px;
  }
}

</style>