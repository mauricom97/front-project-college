<template>
    <div>
        <q-card color="bg-orange-7">
            <q-card-section>
                <q-input filled v-model="user.name" class="q-mt-lg" label="Nome completo" />
                <q-input filled v-model="user.email" class="q-mt-lg" label="E-mail" />
                <q-input v-model="user.date_birth" class="q-mt-lg" filled type="date" hint="Data de nascimento" />
    
                <q-input v-on:keyup="validPassword()" v-model="user.password1" class="q-mt-lg" filled :type="isPwd ? 'password' : 'text'" hint="Insira a nova senha">
                    <template v-slot:append>
                      <q-icon
                        :name="isPwd ? 'visibility_off' : 'visibility'"
                        class="cursor-pointer"
                        @click="isPwd = !isPwd"
                      />
</template>
      </q-input>
      <q-linear-progress :value="progress" rounded :color="color_password_progress" class="q-mt-sm" />
      <q-input v-model="user.password2" class="q-mt-lg" filled :type="isPwd ? 'password' : 'text'" hint="Confirme a senha">
<template v-slot:append>
    <q-icon :name="isPwd ? 'visibility_off' : 'visibility'" class="cursor-pointer" @click="isPwd = !isPwd" />
</template>
      </q-input>
        <q-card-actions class="q-px-md">
          <q-btn unelevated color="primary" size="lg" label="Cadastrar" class="full-width" v-on:click="cadastroLogin()" />
          <q-btn unelevated color="blue-grey-8" size="lg" label="Voltar para login" class="full-width q-mt-lg" v-on:click="login()" />
        </q-card-actions>
      </q-card-section>
    </q-card>

    <q-chat-message id="message" v-if="message" name="Friend" avatar="../../public/img/toasty.png" :text="[message]" sent />
</div>

</template>

<script>
import axios from "axios";
import { ref } from 'vue'
export default {
    props: {
        cadastroLoginIsFalse: Object
    },
    setup() {
        const progress = ref(0.0)

        return {
            progress,
            randomize() {
                progress.value = Math.random()
            }
        }
    },
    data() {
        return {
            user: {
                name: null,
                email: null,
                date_birth: null,
                password1: null,
                password2: null,
            },
            dataUser: {},
            color_password_progress: "red-8",
            isPwd: true,
            message: null
        }
    },

    methods: {
        async cadastroLogin() {

            this.dataUser.user_name = this.user.name
            this.dataUser.user_email = this.user.email,
                this.dataUser.date_birth = this.user.date_birth,
                this.dataUser.user_password = this.user.password1

            if (this.user.password1 != this.user.password2) {
                alert("A senha de confirmação não corresponde com a senha definida")
            } else if (this.progress < 0.5) {
                alert("Senha fraca")
            } else {
                let verifyIdade = await this.verifyBirth(Date.parse(this.dataUser.date_birth), Date.parse(new Date()))
                console.log(verifyIdade)
                if (verifyIdade == "maior") {
                    var config = {
                        method: 'post',
                        url: 'http://localhost:3352/users',
                        headers: {
                            'Content-Type': 'application/json'
                        },
                        data: this.dataUser
                    };

                    await axios(config)
                        .then((response) => {
                            if (response.data.message && response.data.message.error) {
                                this.messageCreateLogin(response.data.message.error)
                            }
                            if (response.data.response && response.data.response.success) {
                                this.messageCreateLogin("Usuário criado com sucesso 😁")
                                this.limparDados()
                            }
                            console.log(JSON.stringify(response.data));
                        })
                        .catch((error) => {
                            console.log(error);
                        });
                } else {
                    this.messageCreateLogin("O usuário não tem idade minima para o cadastro 🤔")
                }

            }


        },

        messageCreateLogin(message) {
            this.message = message
            setTimeout(() => {
                this.message = null
            }, 5000)
        },

        validPassword() {
            if (this.user.password1.length === 0) {
                this.progress = 0.0
            }
            if (this.user.password1.length > 2) {
                this.progress = 0.1
                this.color_password_progress = "red-8"
            }
            if (this.user.password1.length > 8) {
                this.progress = 0.5
                this.color_password_progress = "yellow-5"
            }
            if (this.user.password1.length > 15) {
                this.progress = 10
                this.color_password_progress = "green-13"
            }
        },

        limparDados() {
            this.user = this.clear_object(this.user);
        },
        clear_object(obj) {
            for (const key in obj) {
                obj[key] =
                    typeof obj[key] === "object" ?
                    Array.isArray(obj[key]) ? [] :
                    this.clear_object(obj[key]) :
                    null;
            }
            this.progress = 0.0
            return obj;
        },

        async verifyBirth(birth, today) {
            console.log(birth)
            console.log(today)
            let verify = today - birth
            console.log(verify)
            console.log(567648000000)
            if (verify < 567648000000) {
                return "menor"
            } else {
                return "maior"
            }
        },

        login: function() {
            this.$emit('cadastroLoginIsFalse', { "response": "false" })
        }
    },
}
</script>

<style>

</style>