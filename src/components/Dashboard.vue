<template>
  <div class="hello">
    <div class="container">
      <div class="row">
        <div class="col-md-8 col-md-offset-2">
          <h1 class="page-header">Blockstack Todo App
            <img :src="user.avatarUrl() ? user.avatarUrl() : '/avatar-placeholder.png'" class="avatar">
            <small><span class="sign-out">(<a href="#" @click.prevent="signOut">Sign Out</a>)</span></small>
          </h1>
          <h2 class="user-info">
            <small>
              {{ user.name() ? user.name() : 'Nameless Person'}}'s Todos
            </small>
            <small class="pull-right">
            {{ user.username ? user.username : user.identityAddress }}
            </small>

          </h2>
          <form @submit.prevent="addTodo" :disabled="! todo">
            <div class="input-group">
              <input v-model="todo" type="text" class="form-control" placeholder="Write a todo..." autofocus>
              <span class="input-group-btn">
                <button class="btn btn-default" type="submit" :disabled="! todo">Add</button>
              </span>
            </div>
          </form>

          <!-- trying to add spinner here for now... -->
          {{ todos.length > 0 || todos.length === 0 ? todos.length : 'no todos yet'}}
          <br>
          {{ todos.length > 0 || todos.length === 0 ? todos.length : 'no todos yet'}}
          <br>
          <!-- {{ this.data ? 'we have data!!!!' : 'no data yet'}} -->

          <br>
          <!-- {{this.message ? this.message : "no message..."}} -->

          <!-- <div v-if="loading">
             Loading...
          </div>
          <div v-else>
            {{message}}
          </div> -->

          <br></br>

          {{ this.loading ? 'LOADING-1' : 'not loading 1...'}}
          <br></br>
          {{ this.loading ? 'LOADING-2' : 'not loading 2...'}}
          <br></br>
          {{ this.loading ? 'LOADING-3' : 'not loading 3...'}}
          <br></br>
          <!-- <span v-show="loading" class="glyphicon glyphicon-refresh glyphicon-refresh-animate"></span> -->
          <br></br>
          <span v-if="loading" class="glyphicon glyphicon-refresh glyphicon-refresh-animate">LOADDDDDDDINFFFFGGG</span>
          <br></br>
          <!-- <i v-show="loading" class="fa fa-spinner fa-spin">loadinnnnnnnnnggggg</i> -->
          <br></br>
          <!-- <i class="fa fa-spinner fa-spin" style="font-size:24px"></i> -->
          <br></br>
          <!-- trying to add spinner here for now... -->

          {{ todos.length > 0 || todos.length === 0 ? todos.length : 'no todos yet'}}
          <br>
          {{ todos.length ? todos.length : 'no todos yet'}}
          <ul class="list-group" v-if="todos.length">
            <li v-for="todo in todos"
              class="list-group-item"
              :class="{completed: todo.completed}"
              :key="todo.id">
              <label>
                <input type="checkbox" v-model="todo.completed">{{ todo.text }}
              </label>
              <a @click.prevent="todos.splice(todos.indexOf(todo), 1)"
                class="delete pull-right"
                href="#">X</a>
            </li>
          </ul>
          <p v-else-if="todos.length">No todos!</p>
          <p v-if="! todos.length">SPINNER HERE</p>
          <p v-else>spinner...</p>

        </div>
      </div>
    </div>
  </div>
</template>

<script>
var STORAGE_FILE = 'todos.json'

export default {
  name: 'dashboard',
  props: ['user'],
  // message: 'Hello Vue!',
  // loading: true, // adding this....
  data () {
    return {
      blockstack: window.blockstack,
      todos: [],
      todo: '',
      loading: true, // adding this....
      uidCount: 0
    }
  },
  watch: {
    todos: {
      handler: function (todos) {
        const blockstack = this.blockstack
        const encrypt = true
        return blockstack.putFile(STORAGE_FILE, JSON.stringify(todos), encrypt)
      },
      deep: true
    }
  },
  beforeMount () {
    // console.log('up here..... default.props is: ', this.default.props)

    console.log('0. beforeMount, this.todos is: ', this.todos) // trying this........
    console.log('0. beforeMount, this.data is: ', this.data) // trying this........
  },
  mounted () {
    console.log('0. mounted, calling this.fetchData()....')
    this.fetchData()
    console.log('1. mounted, called this.fetchData()....')
  },
  methods: {
    addTodo () {
      if (!this.todo.trim()) {
        return
      }
      this.todos.unshift({
        id: this.uidCount++,
        text: this.todo.trim(),
        completed: false
      })
      this.todo = ''
    },

    fetchData () {
      console.log('00. fetchData called!')
      console.log('00. fetchData called! todos is::: ', this.todos)
      const blockstack = this.blockstack
      const decrypt = true
      blockstack.getFile(STORAGE_FILE, decrypt)
      .then((todosText) => {
        var todos = JSON.parse(todosText || '[]')
        todos.forEach(function (todo, index) {
          todo.id = index
        })
        this.uidCount = todos.length
        this.todos = todos
        this.loading = false // adding this....
      })
    },

    signOut () {
      this.blockstack.signUserOut(window.location.href)
    }
  }
}
console.log('default is: ', this.default)
console.log('down here..... default.props is: ', this.default.props)
console.log('this.data is: ', this.data)
// console.log('data is: ', this.data)
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>

input::placeholder {
  color: grey;
}

label {
  margin-bottom: 0;
  // width: 100%;
  cursor: pointer;
  input[type="checkbox"] {
    margin-right: 5px;
  }
}
.list-group-item {
  &.completed label {
    text-decoration: line-through;
  }

  .delete {
    display: none;
  }

  &:hover .delete {
    display: inline;
    color: grey;
    &:hover {
      text-decoration: none;
      color: red;
    }
  }
}
</style>
