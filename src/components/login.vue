<template>
    <q-page class="bg-orange-7 row justify-center items-center top-0">
        <div class="cadastro" v-if="cadastro">
            <createLogin style="position: absolute; top: 2%; left: 43%; width: 80%; max-width: 80%;"></createLogin>
        </div>

        <div class="login" v-if="!cadastro">
                    <div class="column">
            <div class="row">
                <h5 class="text-h5 text-white q-my-md">Sistema Corp</h5>
            </div>
            <div class="row">
                <q-card square bordered class="q-pa-lg shadow-1">
                    <q-card-section>
                        <q-input square filled clearable class="q-mt-md" v-model="user_email" label="E-mail" />
                        <q-input square filled clearable class="q-mt-md" type="password" v-model="user_password" label="Senha" />
                        
                    </q-card-section>
                    <q-card-actions class="q-px-md">
                        <q-btn unelevated color="orange-7" size="lg" label="Login" class="full-width" v-on:click="login()" />
                        <q-btn unelevated color="blue-grey-8" size="lg" label="Não tenho cadastro" class="full-width q-mt-lg" v-on:click="loginCadastro()" />
                    </q-card-actions>
                </q-card>
            </div>
        </div>
        </div>
    </q-page>
</template>

<script>
import axios from "axios";
import createLogin from './createLogin.vue'
export default {
components: {
    createLogin
  },
props:{
    loginUser: Object
  },
    data(){
        return{
            user_email: null,
            user_password: null,
            casdastro: false
        }
    },
    methods: {
        loginCadastro(){
            this.cadastro = true
        },
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
                if(response.data.response.success){
                    this.userLogin()
                }
            })
                .catch((error) => {
                console.log(error);
            });
        },
        userLogin: function(){
            this.$emit('loginUser', {"response":"true"})
        }
    }
}
</script>

<style>
q-card{
    width:45%
}
</style>