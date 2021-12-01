<template>
    <q-page class="bg-light-green row justify-center items-center top-0">
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
                        <q-btn unelevated color="light-green-7" size="lg" label="Login" class="full-width" v-on:click="login()" />
                    </q-card-actions>
                </q-card>
            </div>
        </div>
    </q-page>
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
.q-card {
  width: 360px;
}
</style>