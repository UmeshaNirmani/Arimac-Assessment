<template>
  <container fluid class="main">
    <div class="addBtn">
      <router-link to="/add/null" type="button" class="add btn btn-success">Add User</router-link>
    </div>

    <!-- spinner -->
    <Spinner v-if="loading" />

    <!-- error message -->
    <div class="row mt-4">
      <div class="col">
        <p class="text-danger fw-bold text-center" v-if="!loading && errorMessage">
          {{ errorMessage }}
        </p>
      </div>
    </div>

    <div class="card-group" v-if="people.length > 0">
      <div class="row">
        <div class="card" style="width: 18rem" v-for="(person, key) in people" :key="key">
          <img class="card-img-top" :src="`https://avatars.dicebear.com/v2/avataaars/${person.username}.svg?options[mood][]=happy`" />
          <h5 class="card-title">
            {{ person.name }}
          </h5>
          <div class="card-text">
            <i class="fa-solid fa-envelope"> </i>
            <p class="icon">
              {{ person.email }}
            </p>
            <br />

            <i class="fa-solid fa-phone"></i>
            <p class="icon">
              {{ person.phone }}
            </p>
            <br />

            <i class="fa-solid fa-globe"></i>
            <p class="icon">
              {{ person.website }}
            </p>
          </div>
          <hr />
          <p id="card-text">
            <button type="button" class="btn" @click="favouriteUser(person.id)">
              <i class="fa-solid fa-heart" :class="{ clicked: person?.favorite === true ? true : false }"></i>
            </button>

            <button type="button" class="btn" @click="editUser(person.id)">
              <i class="fa-solid fa-pen-to-square"></i>
            </button>

            <button type="button" class="btn" @click="deleteUser(person.id)">
              <i class="fa-solid fa-trash-can"></i>
            </button>
          </p>
        </div>
      </div>
    </div>
  </container>
</template>

<script>
import UserService from "../services/users";
import Spinner from "@/components/Spinner.vue";

export default {
  name: "HomeView",
  components: { Spinner },
  data: function () {
    return {
      loading: false,
      people: [],
      errorMessage: null,
    };
  },
  created: async function () {
    try {
      this.loading = true;
      let response = await UserService.getAllUsers();
      this.people = response.data;
      this.loading = false;
    } catch (error) {
      this.errorMessage = error;
      this.loading = false;
    }
  },
  methods: {
    deleteUser: async function (id) {
      try {
        this.loading = true;
        let response = await UserService.deleteUser(id);

        if (response) {
          let response = await UserService.getAllUsers();
          this.people = response.data;
          this.loading = false;
        }
      } catch (error) {
        this.errorMessage = error;
        this.loading = false;
      }
    },

    favouriteUser: async function (id) {
      try {
        let user = await UserService.getUserById(id);
        let response = await UserService.favouriteUser(user.data.id, { "favorite": true });
        console.log(response);
        let result = await UserService.getAllUsers()
        this.people = result.data;
      } catch (error) {
        console.log(error);
      }
    },

    editUser: async function (id) {
      try {
        let user = await UserService.getUserById(id);
        this.$router.push({ path: '/add/:', query: { id: id } })
        console.log(user);
      } catch (error) {
        console.log(error);
      }
    }
  },
};
</script>

<style>
.addBtn {
  display: flex;
  justify-content: center;
  margin-top: 20px;
}

.card-group {
  padding: 8%;
}

.card {
  margin: 20px;
}

#card-text {
  display: flex;
  justify-content: space-around;
}

.icon {
  display: inline;
  margin-left: 10px;
}

.clicked {
  color: red;
}
</style>
