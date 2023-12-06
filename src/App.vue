<script>
import TodoList from './components/TodoList.vue';
export default{
  name: 'App',
  components:{
    TodoList,

  }
}


</script>

<template>
  <div  class="container">

    <TodoList />

  </div>
</template>

<style>
*{
  box-sizing: border-box;
}
#app{
  font-family: 'Avenir', Arial, Helvetica, sans-serif
;
color:#2c3e50;
font-size: 24px;
}
.container{
  max-width: 600px;
  margin:0 auto;
  text-align: center;

}

</style>
