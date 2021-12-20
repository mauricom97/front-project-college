<template>
    <q-page class="bg-orange-7 top-0">
        <div class="row items-center" style="min-height: 100vh;">
            <div class="col-12">
    
                <div transition-show="jump-down" class="cadastro" v-if="cadastro">
                    <div class="row justify-center">
                        <div class="col-4">
                            <createLogin @cadastroLoginIsFalse="getFormLogin"></createLogin>
    
                        </div>
                    </div>
                </div>
    
                <div class="row justify-center">
                    <div class="col-4">
                        <div transition-show="jump-down" class="login" v-if="!cadastro">
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
                    </div>
                </div>
            </div>
            <q-chat-message id="message" v-if="message" name="Friend" avatar="../../public/img/toasty.png" :text="[message]" sent />
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
    props: {
        loginUser: Object
    },
    data() {
        return {
            user_email: null,
            user_password: null,
            cadastro: false,
            message: false
        }
    },
    methods: {
        loginCadastro() {
            this.cadastro = !this.cadastro
        },
        async login() {
            var config = {
                method: 'post',
                url: 'http://localhost:3352/users/login',
                headers: {
                    'Content-Type': 'application/json'
                },
                data: { "user_email": this.user_email, "user_password": this.user_password }
            };
            await axios(config)
                .then((response) => {
                    
                    console.log(JSON.stringify(response.data));

                    if (response.data.message && response.data.message.error) {
                        this.errorMessage(response.data.message.error)
                    }
                    if (response.data.response && response.data.response.success) {
                        if(response.data.response && response.data.response.success.active){
                            this.userLogin(response.data.response.success)
                        }else{
                            this.errorMessage("O usuário não esta ativo")
                        }
                        
                    }
                })
                .catch((error) => {
                    console.log(error);
                });
        },
        errorMessage: function(message) {
            this.message = message
            setTimeout(() => {
                this.message = null
            }, 5000)
        },
        userLogin: function(data) {
            this.$emit('loginUser', { "response": "true", "data": data })
        },
        getFormLogin: function(value) {
            console.log(value)
            this.cadastro = false
            console.log(this.cadastro)
        }
    }
}
</script>

<style>
q-card {
    width: 45%
}

#message {
    position: absolute;
    bottom: 10%;
    right: 5%;
}
</style>