<template>
   <h1>Todo List</h1>
  <div>
   <div class = "wrapper">
     
      <input
        type="text"
        class="todo-input"
        placeholder="What needs to be done"
        v-model="newTodo"
        @keyup.enter="addTodo"
        v-focus/>
        <button @click="addTodo" class="todo-btn">Add todo</button>
   </div>
      <transition-group name = "fade" enter-active-class = "animate__animated animate__fadeInUp" leave-active-class= " animate__animated animate__fadeOutDown">
  <div v-for="(todo, index) in todosFilter" :key="todo.id" class="todo-item">
    <div class="todo-item-left">
      <input type="checkbox" v-model="todo.completed">
      <div class="todo-item-label" :class="{completed :todo.completed}" v-if="!todo.editing" @dblclick="editTodo(todo)">
        {{ todo.title }}
      </div>
      <input
        v-else
        type="text"
        v-model="todo.title"
        class="todo-item-edit"
        @blur="doneEdit(todo)"
        @keyup.enter="doneEdit(todo)"
        @keyup.esc="cancelEdit(todo)"
        v-focus
      />
    </div>
    <div class="remove-item" @click="$event => removeTodo(index)">&times;</div>
  </div>
  </transition-group>
  <div class="extra-container">
  <div>
    <label>
      <input type="checkbox" :checked="!anyRemaining" @change="checkAllTodos">Check All </label>
  </div>
  <div>{{remaining}} items left</div>
  </div>
  <div class="extra-container">
    <div class='wrapper-btn'>
      <button :class="{active: filter == 'all'}" @click="filter = 'all'">All</button>
      <button :class="{active: filter == 'active'}" @click = "filter = 'active'">Active</button>
      <button :class ="{active: filter == 'completed'}" @click="filter = 'completed'">Completed </button>
    </div>
    <div>
     <transition name = "fade">
      <button v-if="showClearCompletedButton" @click="clearCompleted">Clear Completed</button>
    </transition>
    </div>
  </div>
  </div>
</template>

<script>
import 'animate.css';
export default {
  name: 'todo-list',
  data() {
    return {
      newTodo: '',
      idForTodo: 3,
      beforeEditCache: '',
      filter:'all',
      todos: JSON.parse(localStorage.getItem('todos')) || [],
    };
  },
  directives: {
    //to focus input
    focus: {
      mounted: (el) => el.focus(),
    },
  },
   computed:{
      remaining(){
        return this.todos.filter(todo => !todo.completed).length
      },
      anyRemaining(){
        return this.remaining != 0
      },
      todosFilter(){
        if(this.filter == 'all'){
          return this.todos
        }else if(this.filter == 'active'){
          return this.todos.filter(todo => !todo.completed)
        }else if (this.filter == 'completed'){
          return this.todos.filter(todo => todo.completed)
        }
        return this.todos
      },
      showClearCompletedButton(){
        return this.todos.filter(todo => todo.completed).length >0
      },
    },
  methods: {
    addTodo() {
      if (this.newTodo.trim().length === 0) {
        return; //remove empty todo
      }
      this.todos.push({
        id: this.idForTodo,
        title: this.newTodo,
        completed: false,
        editing: false,
      });
      this.newTodo = '';
      this.idForTodo++;
      this.saveToLocalStorage();
    },
   
    editTodo(todo) {
      this.beforeEditCache = todo.title;
      todo.editing = true; //to edit todo on doubleclick
    },
    doneEdit(todo) {
      if (todo.title.trim() === '') {
        todo.title = this.beforeEditCache; // Revert the title to the previous value
        todo.editing = false;
      } else {
        todo.editing = false;
      }
      this.saveToLocalStorage();
    },
    cancelEdit(todo) {
      todo.title = this.beforeEditCache;
      todo.editing = false;
    },
    removeTodo(index) {
      this.todos.splice(index, 1);
      this.saveToLocalStorage();
    },
    checkAllTodos(event){
      this.todos.forEach((todo)=>todo.completed= event.target.checked)
      this.saveToLocalStorage();
    },
    saveToLocalStorage(){
      localStorage.setItem('todos', JSON.stringify(this.todos))
    },
    clearCompleted(){
      this.todos = this.todos.filter(todo => !todo.completed)
    }
  },
};
</script>


<style>
.wrapper{
  display: flex;
  

}
.todo-input{
 width: 100%;
 padding: 10px 18px;
 font-size: 18px;
 margin-bottom: 16px;
}
.todo-input:focus{
border: 2px solid green;
  outline: 0;
}
.todo-item{
  display: flex;
  justify-content: space-between;
  margin-bottom: 18px;
  font-size: 18px;
  animation-duration:0.2s
  
}
.todo-item-left{
  display: flex;
  align-items: center;
}
.todo-item-label{
  padding: 10px;
  border:1px solid white;
  margin-left: 12px;
}
.todo-item-edit{
  font-size: 24px;
  color: #2c3e50;
  margin-left: 12px;
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  font-family: 'Avenir',Arial, Helvetica, sans-serif;
}
.todo-item-edit:focus{
  outline: none;
}
.todo-btn{
width: 25%;
 padding: 10px 18px;
 font-size: 18px;
 margin-bottom: 16px;
 cursor: pointer;
}
.remove-item{
  cursor: pointer;
  margin-left:14px;

}
.remove-item:hover{
  color: black;
}
.completed{
text-decoration:line-through;
color: #ccc;
}
.extra-container{
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 16px;
  margin-bottom: 14px;
  margin-top: 14px;
  /* border-top: 1px solid grey; */

}
.wrapper-btn{
  display: flex;
  gap: 8px;
}
button {
    font-size: 14px;
    background-color: white;
    appearance: none;
    border: 1px solid grey;
    border-radius: 6px;
    padding: 6px;
  }
  button:hover{
      background: lightgreen;
    }

  button:focus {
      outline: none;
    }
  

  .active {
    background: lightgreen;
  }

/* css transition */
.fade-enter-active, .fade-leave-active {
    transition: opacity 0.2s;
  }

  .fade-enter, .fade-leave-to {
    opacity: 0;
  }
</style>
