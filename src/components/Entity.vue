<template>
<div style="position:absolute; width:30%;" >
  <q-radio v-model="shape" val=false label="Pessoa fisica" />
  <q-radio v-model="shape" val=true label="Pessoa juridica" />

  <div id="isCompany">
  
    <q-input filled v-model="entity.cpf_cnpj" label="CPF/CNPJ" style="margin: 2%;" />
    <q-input filled v-model="entity.corporate_name" label="Razão social" style="margin: 2%;" />
    <q-input filled v-model="entity.fantasy_name" label="Nome fantasia" style="margin: 2%;" />
    <q-input filled v-model="entity.cep" label="CEP" style="margin: 2%;" />
    <q-input filled v-model="entity.address" label="Endereço" style="margin: 2%;" />
    <q-input filled v-model="entity.state" label="Estado" style="margin: 2%;" />
    <q-input filled v-model="entity.phone" label="Celular" style="margin: 2%;" />
    <q-input filled v-model="entity.email" label="E-mail" style="margin: 2%;" />
    <q-input filled v-model="entity.city" label="Cidade" style="margin: 2%;" />

  </div>


  <div style="margin: 2%;">
    <q-btn label="Submit" v-on:click=create color="primary"/>
    <q-btn label="Reset" type="reset" color="primary" flat class="q-ml-sm" />
  </div>  

</div>
</template>

<style>
</style>

<script>

import { ref } from 'vue'
import axios from "axios"

export default {
    setup () {
    return {
      shape: ref('false')
    }
  },
  data(){
    return{
      "entity": {
        "company": null,
        "fantasy_name": null,
        "corporate_name": null,
        "cep": null,
        "address": null,
        "state": null,
        "city": null,
        "cpf_cnpj": null,
        "phone": null,
        "email": null,
        "type":{
            "client": true,
            "agency": false
        }
      }
    }
  },

  methods:{
    
    async create(){
      this.entity.company = this.shape
     await axios.post("http://localhost:3352/entities", this.entity).then((response) => {
       console.log(response)
     }).catch((error) => {
       console.log(error)
     })
    }
  }
}
</script>
