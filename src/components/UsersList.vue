// eslint-disable-next-line vue/multi-word-component-names
<template>
  <body class="bg-dark">
    <nav class="navbar navbar-expand-lg text-white bg-secondary">
      <a class="navbar-brand text-white" href="#">Vue3 ListUsersApp</a>
    </nav>
    <div class="container py-5">
      <!-- Content here -->
      <table class="table table-hover table-dark">
        <thead>
          <tr>
            <th scope="col">#</th>
            <th scope="col">First</th>
            <th scope="col">Last</th>
            <th scope="col">Handle</th>
            <th scope="col">Options</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="user in users" :key="user.id">
            <th scope="row">{{ user.id }}</th>
            <td>{{ user.name }}</td>
            <td>{{ user.username }}</td>
            <td>{{ user.website }}</td>
            <td><button class="btn btn-info btn-block">Update</button></td>
            <td><button @click="deleteUsers(user.id , $event)" class="btn btn-danger btn-block">Delete</button></td>
          </tr>
        </tbody>
      </table>
    </div>
  </body>
</template>

<script>

export default {
  // eslint-disable-next-line vue/multi-word-component-names
  data() {
    return {
      users: [],
      user: {
        name: '',
        username: '',
        email: '',
      },
      operation: "Register",
      userIndex: -1
    }
  },
  created() {
    if (localStorage.getItem('vue3.users') !== null) {
      this.users = JSON.parse(localStorage.getItem('vue3.users'));
    } else {
      this.listUsers();
    }
  },
  mounted() {
    
  },
  methods: {
    listUsers: async function() {
      const res = await fetch("https://jsonplaceholder.typicode.com/users");
      const data = await res.json();
      this.users = data.slice(0,5)
      this.updateLocalStorage();
    },
    updateLocalStorage: function () {
      localStorage.setItem('vue3.users', JSON.stringify(this.users));
    },
    updateUsers: function() {

    },
    deleteUsers: function(id, event) {
      const confirmation = confirm("Do you want to delete user?");
      
      if (confirmation) {
        this.users = this.users.filter(user => user.id !== id)
        this.updateLocalStorage();
      } else {
        event.preventDefault();
      }

    }
  }
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
