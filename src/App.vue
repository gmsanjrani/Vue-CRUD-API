<template>
  <!-- ==== Navbar ==== -->
  <nav class="navbar navbar-dark bg-mynav">
    <div class="container-fluid">
      <a class="navbar-brand" href="#">My App</a>
    </div>
  </nav>

  <!-- ========= Create Model ======= -->

  <!-- === List === -->
  <div class="container">
    <div class="d-flex bd-highlight mb-3">
      <div class="me-auto p-2 bd-highlight"><h2>Users</h2></div>
      <div class="p-2 bd-highlight">
        <button type="button" class="btn btn-secondary" @click="createUser">
          Create
        </button>
      </div>
    </div>

    <div class="table-responsive">
      <table class="table">
        <thead>
          <tr>
            <th scope="col">#</th>
            <th scope="col">Name</th>
            <th scope="col">Email</th>
            <th scope="col">Website</th>
            <th scope="col">Edit</th>
          </tr>
        </thead>
        <tbody id="mytable">
          <tr v-for="(user, index) in users" :key="user.id">
            <td>{{ index + 1  }}</td>
            <td>{{ user.attributes.name }}</td>
            <td>{{ user.attributes.email }}</td>
            <td>{{ user.attributes.website }}</td>
            <td>
              <button
                type="button"
                class="btn btn-outline-secondary"
                @click="updateUser(user)"
              >
                Edit
              </button>
              <button
                type="button"
                style="margin-left: 3px"
                class="btn btn-outline-danger"
                @click="deleteMessage(user)"
              >
                Delete
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      users: [],
      formData: {
        name: "",
        email: "",
        website: "",
      },
    };
  },

  methods: {
    async deleteMessage(user) {
      await this.$swal({
        title: `You wanna Delete this User: ${user.attributes.name}!`,
        // showCloseButton: true,
        showCancelButton: true,
        cancelButtonText: "NO",
        confirmButtonText: "YES",
      });
      await axios.delete(
        `http://localhost:1337/api/peoples/${user.id}`
      );
      const newUsers = this.users.filter((u) => {
        return u.id !== user.id;
      });
      this.users = newUsers;
    },


    async createUser() {
      const x = await this.$swal({
        title: "Create user",
        html:
          `<input id="id" type="hidden">` +
          '<input id="name" class="swal2-input" placeholder="Enter Name">' +
          '<input id="email" class="swal2-input" placeholder="Enter Email">' +
          '<input id="website" class="swal2-input" placeholder="Enter Website">',
        showCancelButton: true,
        cancelButtonText: "Cancel",
        confirmButtonText: "Confirm",
        focusConfirm: false,
        preConfirm: () => {
          return [
            document.getElementById("name").value,
            document.getElementById("email").value,
            document.getElementById("website").value,
          ];
        },
      });

      // this.formData.id = this.users.length + 1;
      this.formData.name = x.value[0];
      this.formData.email = x.value[1];
      this.formData.website = x.value[2];

      console.log(x.value[2])
      // this.users.push(this.formData);
      const len = this.users.length+1
      console.log(len)

      const res = await axios.post(
        "http://localhost:1337/api/peoples",
        {
          data:this.formData
        }
      );
      console.log(res);

      this.getUsers()
    },


    async updateUser(user) {
      const x = await this.$swal({
        title: "Create user",
        html:
          `<input id="id" value="${user.id}" type="hidden">` +
          `<input id="name" class="swal2-input" value="${user.attributes.name}" placeholder="Enter Name">` +
          `<input id="email" class="swal2-input" value="${user.attributes.email}" placeholder="Enter Email">` +
          `<input id="website" class="swal2-input" value="${user.attributes.website}" placeholder="Enter Website">`,
        focusConfirm: false,
        showCancelButton: true,
        cancelButtonText: "Cancel",
        confirmButtonText: "Update",
        preConfirm: () => {
          return [
            document.getElementById("name").value,
            document.getElementById("email").value,
            document.getElementById("website").value,
          ];
        },
      });


      for (let i = 0; i < this.users.length; i++) {
        if (this.users[i].id == user.id) {
          this.users[i].attributes.name = x.value[0]
          this.users[i].attributes.email = x.value[1]
          this.users[i].attributes.website = x.value[2]
        }
      }

      this.formData.name = x.value[0];
      this.formData.email = x.value[1];
      this.formData.website = x.value[2];

      const res = await axios.put(`http://localhost:1337/api/peoples/${user.id}`,
        {
          data: this.formData
        }
      );
      console.log(res);
    },

    
    getUsers() {
      axios
        .get("http://localhost:1337/api/peoples")
        .then((res) => {
          console.log(res.data.data)
          this.users = res.data.data
        })
        .catch((e) => console.log(e));
    },
  },
  mounted() {
    this.getUsers();
  },
};
</script>

<style>
.bg-mynav {
  background-color: #2c3e50;
}

body {
  font-size: 1.25rem;
  background-color: #f6f8fa;
}

td {
  line-height: 3rem;
}
</style>
