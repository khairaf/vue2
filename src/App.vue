<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">Logout</router-link>
    </div>
  
  <div class="card container mb-3">
  <div class="card-body" size="12">
  <b-form @submit.prevent="login">
  <b-form-group label="Registered email address:" label-for="input-1" description="We'll never share your email with anyone else.">
	<b-form-input id="input-1" v-model="masuk.email" type="email" required placeholder="Enter email"></b-form-input>
  </b-form-group>
  <b-form-group label="Your Password:" label-for="input-2">
	<b-form-input type="password" id="text-password" aria-describedby="password-help-block" v-model="masuk.password" required placeholder="Enter password"></b-form-input>
  </b-form-group>
  <b-button type="submit" variant="success" size="sm">Login</b-button>  <b-button type="submit" variant="success" size="sm" @click="auto">Easy Login</b-button>   
  </b-form> 
  </div>
  </div>
  
  <p style="color: red" >{{message}}</p> 
  
  <div class="card container mb-3" >
  <div class="card-body" size="12">
  <b-form @submit.prevent="add">
  <b-form-group  label="Register A New User:">
	<b-form-input v-model="form.name" required placeholder="Enter your full name"></b-form-input>
  </b-form-group>
  <b-form-group label="Email Address:">
	<b-form-input v-model="form.email" type="email" required placeholder="Enter your email address"></b-form-input>
  </b-form-group>
  <b-form-group label="Phone Number:">
	<b-form-input v-model="form.phone" required placeholder="Enter your phone number"></b-form-input>
  </b-form-group>
  <b-form-group label="Password:">
	<b-form-input v-model="form.password" type="password" required placeholder="Enter password"></b-form-input>
  </b-form-group>
  <b-form-group label="Gender:">
	<b-form-input v-model="form.gender" required placeholder="Enter your gender"></b-form-input>
  </b-form-group>
  <b-form-group label="Active ?:">
	<b-form-input v-model="form.is_active" required placeholder="Type 0 or 1"></b-form-input>
  </b-form-group>
  <b-form-group label="Address:" label-for="input-2">
	<b-form-input v-model="form.address" required placeholder="Enter your address"></b-form-input>
  </b-form-group>
  <b-button type="submit" variant="success" size="sm" v-show="!updateSubmit">Add new user</b-button>
  <b-button type="button" variant="success" size="sm" v-show="updateSubmit" @click="update(form)">Update data</b-button>     
  </b-form>   
  </div>
  </div>
  
  <div class="card container mb-3" >
  <div class="card-body" size="12">
  <h1 style="color: #42b983;">LIST OF ALL OF USERS..</h1>
  
  <ul v-for="user in users" :key="user.id" class="list-group">
    <li class="list-group-item d-flex justify-content-between align-items-center">
	{{user.name}}
	<button @click="edit(user)" class="badge badge-secondary badge-pill" style="float: left"></button>
    <button style="color: red" type="button" class="close" aria-label="Close" @click="del(user)"><span aria-hidden="true">&times;</span></button>
	<button @click="showDetail(user)" class="badge"><span aria-hidden="true">&times;</span></button>
	</li>
  </ul>
  
  </div>
  </div>
  
  
  </div>
</template>

<script>
/* eslint-disable */ 
import axios from 'axios'
export default {
  data(){
    return{
        masuk: {
          email: '',
          password: ''
        },
		form: {
          name: '',
          email: '',
		  phone: '',
		  password: '',
		  gender: '',
		  is_active: '',
		  address: ''
        },
        users: '',
        updateSubmit: false,
		token: "",
		detail: false,
		detailData : '',
		pageParam: {
		  page: 1,
		  limit: 10
		},		
		message: ''
    }
  },
  mounted() {
   if (localStorage.token) {
      this.token = localStorage.token;
    }
   this.load()
  },
  methods: {
    load(){
	    let {page, limit} = this.pageParam
        axios.get(`https://sample.mikaapp.id/api/users?page=${page}&limit=${limit}`, { headers: {"Authorization" : `Bearer ${this.token}`} }).then(res => {
		this.users = res.data.data
      }).catch ((err) => {
        console.log("errornya apa?", err);
        this.message = "Hai you must hit the login button"
		setTimeout(() => this.message ="", 3000)
      })
    },
	auto() {
	  this.masuk.email = 'user@mika.id'
	  this.masuk.password = 'V9nF54fH/@V:=h%['
	},
    login(){	  
      axios.post('https://sample.mikaapp.id/api/login', this.masuk).then(res => {
          this.token = res.data.data.token;
		  this.load()
          localStorage.setItem('token', this.token)
		  console.log("datanya :", res);
		  console.log(this.token);
		  this.masuk = {}
      }).catch ((err) => {
        console.log("errornya apa?", err);
        this.message = "Hai you must hit the login button"
		setTimeout(() => this.message ="", 3000)
      })
    },
	add(){
	  console.log("form ",this.form)
      axios.post('https://sample.mikaapp.id/api/users', this.form, { headers: {"Authorization" : `Bearer ${this.token}`} }).then(res => {
          this.load()
          this.message = "A new user added to the list"
		  setTimeout(() => {this.message =""; this.form = {} }, 3000)
		  console.log("datanya :", res);
      }).catch ((err) => {
        console.log("tes err", err);
        this.message = "Hai you must provide the correct input"
		setTimeout(() => this.message ="", 3000)
      })
    },
    edit(user){ 
        this.updateSubmit = true
        this.form = user
		console.log("edit: ", user)
    },
    update(form){ 
	   console.log(form)
	   let id = form.id
	   console.log("aidi", id, "thisformname", this.form.name)
       return axios.put(`https://sample.mikaapp.id/api/users/${id}`, this.form.name, { headers: {"Authorization" : `Bearer ${this.token}`} }).then(res => {
	    console.log(res);
        
		this.form = res.data.data
        this.updateSubmit = false
		this.message = `Data for user with id: ${id} updated`
		setTimeout(() => {this.message =""; this.form = {} }, 3000)
      }).catch((err) => {
        console.log("error when updating", err);
    
      })
    },
    del(user){
	  console.log(user)
	  
      axios.delete(`https://sample.mikaapp.id/api/users/${user.id}`, { headers: {"Authorization" : `Bearer ${this.token}`} }).then(res =>{
          
          this.users.splice(this.users.indexOf(user), 1);
	      console.log("thisusers", this.users)
		  this.load()
      }).catch((err) => {
        console.log(err);
      })
    },
    showDetail(user) {
      axios.get(`https://sample.mikaapp.id/api/users/${user.id}`, { headers: {"Authorization" : `Bearer ${this.token}`} }).then(res =>{
          this.detail =true
          console.log(res)
		  this.detailData = res.data.data
      }).catch((err) => {
        console.log(err);
      })
    },
	nextPage(){
         this.pageParam.page++;
		 console.log("next",this.pageParam.page)
		 this.load()
    },
    prevPage(){
        this.pageParam.page--;
		console.log("prev", this.pageParam.page)
		this.load()
    }
 }
}
</script>



<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;

  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>
