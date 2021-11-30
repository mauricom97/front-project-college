<template>
  <div>
      <q-input filled class="q-mt-md" v-model="user_email" label="E-mail" />
      <q-input filled class="q-mt-md" type="password" v-model="user_password" label="Senha" />
      <q-btn color="primary" label="Login" v-on:click="login()" />
  </div>
</template>

<script>
import axios from "axios";
export default {
    data(){
        return{
            user_email: null,
            user_password: null
        }
    },

    methods: {
        async login(){
            var config = {
            method: 'post',
            url: 'http://localhost:3352/users/login',
            headers: { 
                'Content-Type': 'application/json'
            },
            data : { "user_email": this.user_email, "user_password": this.user_password }
            };

            await axios(config)
            .then((response) => {
            console.log(JSON.stringify(response.data));
            
            })
            .catch((error) => {
            console.log(error);
            });
        }
    }
}
</script>

<style>

</style>