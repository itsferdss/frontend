<template>
  <div class="container">
    <div class="login-form-container">
      <form @submit.prevent="login"> <!-- Bind login method to form submit event -->
        <div id="login">
          <h1 class="title">Accounting Management System</h1>
          <p class="subtitle">Sign in to continue</p>
          <div class="input-group">
            <label class="inputTitle" for="email">Email</label>
            <input type="text" id="email" v-model="email" required> <!-- Update v-model to bind to email -->
          </div>
          <div class="input-group">
            <label class="inputTitle" for="password">Password</label>
            <input type="password" id="password" v-model="password" required> <!-- Update v-model to bind to password -->
          </div>
          <button type="submit">Log in</button> <!-- Change button type to submit -->
          <p>{{ errorMessage }}</p>
        </div>
      </form>
    </div>
    <div class="trapezoid"></div>
  </div>
</template>

<script>
import Swal from 'sweetalert2';
import axios from 'axios';

export default {
  data() {
    return {
      email: '',
      password: '',
      errorMessage: ''
    };
  },
  methods: {
    login() {
      if (!this.email || !this.password) {
        Swal.fire({
          icon: "error",
          title: "Oops...",
          text: "Fields cannot be empty!",
        });
      } else {
        axios.get('/login', {
          params: {
            email: this.email,
            password: this.password
          }
        })
          .then(response => {
            const { success } = response.data;
            if (success) {
              this.$router.push('/employee'); // Fixed route name
              Swal.fire({
                position: "top-end",
                icon: "success",
                title: "Successfully logged in.",
                showConfirmButton: false,
                timer: 1500
              });
            } else {
              Swal.fire({
                icon: "error",
                title: "Oops...",
                text: "Invalid credentials. Please try again!",
              });
            }
            this.email = '';
            this.password = '';
          })
          .catch(error => {
            Swal.fire({
              icon: "error",
              title: "Oops...",
              text: "Error logging in. Please try again later!",
            });
          });
      }
    }
  }
};
</script>

<style scoped>
.container {
  margin-top: 170px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 200px;
}

.login-form-container {
  max-width: 500px;
  padding: 20px;
  background-color: #E3F1F8;
  border-radius: 10px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  margin-left: 340px;
}

.input-group {
  margin-bottom: 20px;
}

label {
  display: block;
  margin-bottom: 5px;
  color: #B3D9E6;
}

input[type="text"],
input[type="password"] {
  width: 100%;
  height: 35px;
  padding: 10px;
  border: 1px solid #B3D9E6;
  background-color: #E3F1F8;
}

button {
  width: 100%;
  background-color: #92d1e7;
  color: black;
  border: none;
  padding: 5px;
  border-radius: 5px;
  cursor: pointer;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  font-weight: bold;
  font-size: 20px;
  transition: background-color 0.3s;
}

button:hover {
  background-color: #207BA6;
  color: white;
}

.subtitle {
  color: rgb(53, 53, 53);
  margin: 0;
  font-size: 15px;
  text-align: center;
}

.inputTitle {
  color: rgb(53, 53, 53);
  text-align: left;
  margin-left: 0px;
}

.title {
  font-weight: bold;
  margin: 0;
  font-size: 40px;
  color: rgb(53, 53, 53);
  text-align: center;
}

.trapezoid {
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 70px;
  background-color: #B3D9E6;
  align-items: center;
}
</style>
