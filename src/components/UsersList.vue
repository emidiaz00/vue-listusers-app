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
            <th scope="col">Name</th>
            <th scope="col">User</th>
            <th scope="col">Email</th>
            <th scope="col">Options</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="user in users" :key="user.id">
            <th scope="row">{{ user.id }}</th>
            <td>{{ user.name }}</td>
            <td>{{ user.username }}</td>
            <td>{{ user.website }}</td>

            <td><button v-on:click="this.toggleModal" v-bind:value="operationUpdate" @click="updateUsers(user.id)" class="btn btn-info btn-block">Update</button></td>


            <td><button @click="deleteUsers(user.id , $event)" class="btn btn-danger btn-block">Delete</button></td>
          </tr>
        </tbody>
      </table>
    </div>

    
    
    <div @click="hideRegisterUser()" class="w-50 mx-auto justify-content-center align-items-center">
      
      <div v-show="this.showModal" v-bind:on-open="this.handleOpen" v-bind:on-close="this.handleClose" class="card card-body">
        <form ref="userForm" v-on:submit="createUser">
          <div class="form-group">
            <input type="text" ref="name" v-model="user.name" class="form-control" placeholder="Name"
            minlength="10" maxlength="50" required />
          </div>
          <div class="form-group">
            <input type="text" v-model="user.username" class="form-control" placeholder="Username"
            minlength="6" maxlength="20" required />
          </div>
          <div class="form-group">
            <input type="email" v-model="user.email" class="form-control" placeholder="Email"
            minlength="10" maxlength="50" required />
          </div>
          <div class="form-group">
            <input type="submit" class="btn btn-success btn-block text-dark">
          </div>
          <div class="form-group">
            <input type="reset" class="btn btn-primary btn-block" value="Clear">
          </div>
          <button type="button" class="btn btn-secondary" v-on:click="this.toggleModal" data-dismiss="modal">Close</button>
        </form>
      </div>
      </div>

    <div class="page">
      <bs-modal v-show="this.showModal" v-bind:on-open="this.handleOpen" v-bind:on-close="this.handleClose">
        Some content displayed in the modal.
        <button type="button" class="btn btn-secondary" v-on:click="this.toggleModal" data-dismiss="modal">Close</button>
      </bs-modal>
      <button v-on:click="this.toggleModal">Toggle modal</button>

    </div>
  </body>
</template>

<script>
import ModalComponent from 'vue-bootstrap4-modal'

export default {
  components: {
    'bs-modal': ModalComponent
  },
  // eslint-disable-next-line vue/multi-word-component-names
  data() {
    return {
      users: [],
      user: {
        id: '',
        name: '',
        username: '',
        email: ''
      },
      operation: "Register",
      operationUpdate: "Update",
      userIndex: -1,
      showModal: false,
      showRegisterUser: false,
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
    this.$refs.name.focus();
  },
  methods: {
    toggleModal() {
      this.showModal = ! this.showModal
    },
    handleOpen() {
      console.log('Modal is about to open.');
    },
    handleClose() {
      console.log('Modal has closed.');
      this.showModal = false
    },
    showModalRegisterUser() {
      this.showRegisterUser = true
    },
    hideModalRegisterUser() {
      this.showRegisterUser = false
    },
    listUsers: async function() {
      const res = await fetch("https://jsonplaceholder.typicode.com/users");
      const data = await res.json();
      this.users = data.slice(0,5)
      this.updateLocalStorage();
    },
    updateLocalStorage: function () {
      localStorage.setItem('vue3.users', JSON.stringify(this.users));
    },
    processUser: function (event) {
      event.preventDefault();
      if (this.operation === "Register") {
        this.user.id = this.findMaxId() + 1;
        this.users.push({
          id: this.user.id,
          name: this.user.name,
          username: this.user.username,
          email: this.user.email
        });
      } else {
        this.users[this.userIndex].name = this.user.name;
        this.users[this.userIndex].username = this.user.username;
        this.users[this.userIndex].email = this.user.email;
      }
      this.updateLocalStorage();
      this.findMaxId();
      this.clearFields();
    },
    findMaxId: function () {
      const maxId = Math.max.apply(Math, this.users.map(function (user) {
        return user.id;
      }));
      return maxId;
    },
    updateUsers: function(id, event) {
      event.preventDefault();
      if (this.operationUpdate === "Update") {
        const userFound = this.users.find((user, index) => {
          this.userIndex = index;
          return user.id === id;
        });
        this.user.name = userFound.name;
        this.user.username = userFound.username;
        this.user.email = userFound.email;
        this.updateLocalStorage();
      }
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
h2 {
  color: #fff;
  text-transform: uppercase;
  font-size: 26px;
}
</style>
