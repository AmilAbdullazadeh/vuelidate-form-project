<template>
  <div class="container">
    <h3 class="text-center mt-3">Vuelidate</h3>
    <div class="d-flex justify-content-center align-content-center flex-row">
      <div class="card p-4 mt-3 mr-4  shadow">
        <form style="width: 350px" @submit.prevent="onSubmit">
          <div class="form-group">
            <label>Email</label>
            <input
                @blur="$v.email.$touch()"
                v-model="email"
                type="email"
                class="form-control"
                :class="{'is-invalid' : $v.email.error}"
                placeholder="Email">
            <small v-if="!$v.email.required" class="form-text text-danger">This email field is required</small>
            <small v-if="!$v.email.email" class="form-text text-danger">Please enter your email address</small>
            <small v-if="!$v.email.uniq" class="form-text text-danger">Please enter another email address</small>
          </div>
          <div class="form-group">
            <label>Password</label>
            <input
                v-model="$v.password.$model"
                type="password"
                class="form-control"
                placeholder="Password">
            <small v-if="!$v.password.required" class="form-text text-danger">This password field is required</small>
            <small v-if="!$v.password.numeric" class="form-text text-danger">Please enter your password</small>
            <small v-if="!$v.password.minLength" class="form-text text-danger">
              Your password min length sholud be {{ $v.password.$params.minLength.min }}
            </small>
            <small v-if="!$v.password.maxLength" class="form-text text-danger">
              Your password max length should be {{ $v.password.$params.maxLength.max }}
            </small>
          </div>
          <div class="form-group">
            <label>Repeat Passoword</label>
            <input
                v-model="$v.repassword.$model"
                type="password"
                class="form-control"
                placeholder="Confirm Passoword">
            <small v-if="!$v.repassword.required" class="form-text text-danger">This repeat password field is
              required</small>
            <small v-if="!$v.repassword.numeric" class="form-text text-danger">Please enter your repeat password</small>
            <small v-if="!$v.repassword.minLength" class="form-text text-danger">
              Your password min length sholud be {{ $v.repassword.$params.minLength.min }}
            </small>
            <small v-if="!$v.repassword.maxLength" class="form-text text-danger">
              Your password max length should be {{ $v.repassword.$params.maxLength.max }}
            </small>
          </div>

          <div class="form-group">
            <label>Age</label>
            <input
                v-model="$v.age.$model"
                type="text"
                class="form-control"
                placeholder="Age">
            <small v-if="!$v.age.required" class="form-text text-danger">This age field is required</small>
            <small v-if="!$v.age.numeric" class="form-text text-danger">Please enter your age</small>
            <small v-if="!$v.age.between" class="form-text text-danger">
              Your age should be between {{ $v.age.$params.between.min }} - {{ $v.age.$params.between.max }}
            </small>
          </div>

          <div class="form-group">
            <label>Category</label>
            <select v-model="$v.selectedCategory.$model" class="form-control">
              <option v-for="(category, index) in categories" :key="index" :value="category">{{ category }}</option>
            </select>
            <small v-if="!$v.selectedCategory.checked" class="form-text text-danger">Only Mac OS</small>
          </div>

          <a @click="newHobby" class="text-white btn btn-secondary rounded-0 btn-sm">Add Hobby</a>

          <small v-if="!$v.hobbies.required" class="form-text text-danger">Reqired</small>
          <small v-if="!$v.hobbies.minLength" class="form-text text-danger">Min Length</small>

          <ul class="list-group mt-3 mb-3 border-0">
            <li v-for="(hobby,index) in hobbies" :key="index" class="list-group-item d-flex pl-2">
              <input
                  @blur="$v.hobbies.$each[index].value.$touch()"
                  type="text"
                  class="form-control mr-2"
                  :class="{ 'is-invalid': $v.hobbies.$each[index].$error}"
                  v-model="hobby.value">
              <button class="btn btn-sm btn-danger rounded-0" @click="hobbies.splice(index, 1)">Delete</button>
            </li>
          </ul>

          <button class="btn btn-outline-secondary rounded-0" :disabled="$v.$invalid">Submit</button>
        </form>
      </div>
      <div style="width: 400px" class="card p-4 mt-3  shadow">
        <p>
          {{ $v }}
        </p>
      </div>
    </div>
  </div>
</template>
<script>
import {required, email, numeric, minLength, maxLength, sameAs, between} from "vuelidate/lib/validators"

export default {

  data() {
    return {
      email: null,
      password: null,
      repassword: null,
      selectedCategory: "Developer",
      age: null,
      categories: ["Developer", "Help Desk", "Cloud", "Server", "Unix", "Linux", "Mac OS", "Windows"],
      hobbies: []
    }
  },
  methods: {
    onSubmit() {
      let form = {
        email: this.email,
        password: this.password,
        category: this.category,
        hobbies: this.hobbies,
      }
      console.log(form)
    },
    newHobby() {
      let hobby = {
        id: Math.random() * Math.random() * 1000,
        value: ''
      }
      this.hobbies.push(hobby)
    }
  },
  validations: {
    email: {
      required,
      email,
      uniq: value => {
        return new Promise((resolve) => {
          setTimeout(() => {
            resolve(value !== "amil@gmail.com")
          }, 1000)
        })
      }
    },
    password: {
      required,
      numeric,
      minLength: minLength(6),
      maxLength: maxLength(10)
    },
    repassword: {
      required,
      numeric,
      minLength: minLength(6),
      maxLength: maxLength(10),
      sameAs: sameAs('password')
    },
    age: {
      required,
      numeric,
      between: between(14, 45)
    },
    selectedCategory: {
      checked(val, vm) {
        return vm.selectedCategory === "Mac OS" ? true : false
      }
    },
    hobbies: {
      required,
      minLength: minLength(3),

      $each: {
        value: {
          required,
          minLength: minLength(5)
        }
      }
    }
  }

}
</script>
