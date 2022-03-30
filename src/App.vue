<style>
@import "./assets/style.css";
</style>

<script>

export default {
  async created() {
    await this.loadTodosFromApi();
    await this.loadUsersAndAttach();

    this.todos = this.originalArray.sort( this.sortByStatus);
    this.sortedByStatus = true;
  },

  data() {
    return {
      originalArray: null, /* nulls setup onload in created lifecycle method */
      todos: null ,
      sortedByStatus: null,
      sortBtnTxt: "Sort by Name",
    };
  },

  methods: {
    sortTodosToggle() {
      if (this.sortedByStatus) {
        this.todos = this.todos.sort(this.sortByName);
        this.sortBtnTxt = "Sort by Status"; //  refactor put in constant  
      } else {
        this.todos = this.todos.sort(this.sortByStatus);
        this.sortBtnTxt = "Sort by Name";  // TODO refactor string repeat put in constant
      }

      this.sortedByStatus = !this.sortedByStatus; // invert flag
    },
    async loadTodosFromApi() {
      const url = "https://test1.green-box.co.uk/todos";

      try {
        let response = await fetch(url);
        this.originalArray = await response.json();;
      } catch (error) {
        console.log(error);
      }
    },
    async loadUsersAndAttach(){

      const url = "https://test1.green-box.co.uk/users"; // TODO refactor domain str repeated

      try {
        let response = await fetch(url);
        let users = await response.json();
        for(let i=0; i<this.originalArray.length; i++){                    
          for(let x=0; x<users.length; x++){ //  TODO refactor searching for user on every iteration of loop, could improve this cache users in something with constant time lookup like a hashmap
            if( this.originalArray[i].userId == users[x].id ){
                    this.originalArray[i].personName = users[x].name;
            }
          }
        }
      } catch (error) {
        console.log(error);
      }


    },
    sortByName(a, b) {
      if (a.personName < b.personName) return -1;
      if (a.personName > b.personName) return 1;
      return 0;
    },
    sortByStatus(todo1, todo2) {
      if (todo1.completed < todo2.completed) return -1;
      else if (todo1.completed > todo2.completed) return 1;
      else return 0;
    },
  },
};
</script>

<template>
  <h2>Todos</h2>

  <button @click="sortTodosToggle">
    {{ sortBtnTxt }}
  </button>

  <table class="styled-table">
    <thead>
      <tr>
        <th>ID</th>
        <th>Task</th>
        <th>Completed</th>
        <th>Users name</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="todo in todos" :key="todo.id">
        <td>{{ todo.id }}</td>
        <td>{{ todo.title }}</td>
        <td>{{ todo.completed }}</td>
        <td>{{ todo.personName }}</td>
      </tr>
    </tbody>
  </table>
</template>
