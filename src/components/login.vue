<template>
    <q-page class="bg-primary top-0">
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
                                    <p class="q-my-md text-white" style="font-family: 'Press Start 2P', cursive;">&lt; code_community /&gt;</p>
                                </div>
                                <div class="row">
                                    <q-card square bordered class="q-pa-lg shadow-1" style="border-radios: 9%;">
                                        <q-card-section>
                                            <q-input square filled clearable class="q-mt-md" v-model="user_email" label="E-mail" />
                                            <q-input square filled clearable class="q-mt-md" type="password" v-model="user_password" label="Senha" />
    
                                        </q-card-section>
                                        <q-card-actions class="q-px-md">
                                            <q-btn unelevated color="primary" size="lg" label="Login" class="full-width" v-on:click="login()" />
                                            <q-btn unelevated color="blue-grey-8" size="lg" label="Não tenho cadastro" class="full-width q-mt-lg" v-on:click="loginCadastro()" />
                                            <a style="margin-top: 2%;" href="javascript:void(0);" v-on:click="recoveryPass()">Esqueceu sua senha?</a>
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
        <link rel="preconnect" href="https://fonts.googleapis.com">
        <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
        <link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap" rel="stylesheet">
        <recoveryUser v-if="recoveryPassword"></recoveryUser>
    </q-page>
</template>

<script>
import axios from "axios";
import createLogin from './createLogin.vue';
import recoveryUser from './recoveryUser.vue';
export default {
    components: {
        createLogin,
        recoveryUser
    },
    props: {
        loginUser: Object
    },
    data() {
        return {
            user_email: null,
            user_password: null,
            cadastro: false,
            message: false,
            ifen: false,
            recoveryPassword: true
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
                        if (response.data.response && response.data.response.success.active) {
                            this.userLogin(response.data.response.success)
                        } else {
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
        },
        recoveryPass: function(){
            this.recoveryPassword = !this.recoveryPassword
        }
    },
        beforeCreate(){
            setInterval(() => {
                this.ifen = !this.ifen
            }, 500)
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