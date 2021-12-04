<template>
  <div>
    <q-card class="my-card" color="bg-orange-7">
      <q-card-section>
      <q-input filled v-model="name" class="q-mt-lg" label="Nome completo" />
      <q-input filled v-model="email" class="q-mt-lg" label="E-mail" />
      <q-input v-model="date_birth" class="q-mt-lg" filled type="date" hint="Data de nascimento" />

      <q-input v-on:keyup="validPassword()" v-model="password1" class="q-mt-lg" filled :type="isPwd ? 'password' : 'text'" hint="Insira a nova senha">
        <template v-slot:append>
          <q-icon
            :name="isPwd ? 'visibility_off' : 'visibility'"
            class="cursor-pointer"
            @click="isPwd = !isPwd"
          />
        </template>
      </q-input>
      <q-linear-progress :value="progress" rounded :color="color_password_progress" class="q-mt-sm" />
      <q-input v-model="password2" class="q-mt-lg" filled :type="isPwd ? 'password' : 'text'" hint="Confirme a senha">
        <template v-slot:append>
          <q-icon
            :name="isPwd ? 'visibility_off' : 'visibility'"
            class="cursor-pointer"
            @click="isPwd = !isPwd"
          />
        </template>
      </q-input>
        <q-card-actions class="q-px-md">
          <q-btn unelevated color="orange-7" size="lg" label="Cadastrar" class="full-width" v-on:click="cadastroLogin()" />
          <q-btn unelevated color="blue-grey-8" size="lg" label="Voltar para login" class="full-width q-mt-lg" v-on:click="cadastroLogin()" />
        </q-card-actions>
      </q-card-section>
    </q-card>
  </div>
</template>

<script>
import axios from "axios";
import { ref } from 'vue'
export default {
  setup () {
    const progress = ref(0.0)

    return {
      progress,
      randomize () {
        progress.value = Math.random()
      }
    }
  },
data(){
  return{
    name: null,
    email: null,
    date_birth: null,
    password1: null,
    password2: null,
    dataUser: {},
    color_password_progress: "red-8"
  }
},

methods: {
  async cadastroLogin(){

    this.dataUser.user_name = this.name
    this.dataUser.user_email = this.email,
    this.dataUser.date_birth = this.date_birth,
    this.dataUser.user_password = this.password1

    let verifyIdade = await this.verifyBirth(Date.parse(this.dataUser.date_birth), Date.parse(new Date()))
    console.log(verifyIdade)
    if(verifyIdade == "maior"){
      var config = {
      method: 'post',
      url: 'http://localhost:3352/users',
      headers: { 
        'Content-Type': 'application/json'
      },
      data : this.dataUser
    };

      await axios(config)
      .then((response) => {
        console.log(JSON.stringify(response.data));
      })
      .catch((error) => {
        console.log(error);
      });
    }else{
      alert("Menor")
    }

  },

  validPassword(){
    if(this.password1.length === 0){
      this.progress = 0.0
    }
    if(this.password1.length > 2){
      this.progress = 0.1
      this.color_password_progress = "red-8"
    }
    if(this.password1.length > 8){
      this.progress = 0.5
      this.color_password_progress = "yellow-5"
    }
    if(this.password1.length > 15){
      this.progress = 10
      this.color_password_progress = "green-13"
    }
  },

  async verifyBirth(birth, today){
    console.log(birth)
    console.log(today)
    let verify = today - birth 
    console.log(verify)
    console.log(567648000000)
    if(verify < 567648000000){
        return "menor"
    }else{
        return "maior"
    }
  }
},
}
</script>

<style>

</style>