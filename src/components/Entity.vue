<template>
<div style="position:absolute; width:30%;" >
  <q-radio v-model="shape" val=false label="Pessoa fisica" />
  <q-radio v-model="shape" val=true label="Pessoa juridica" />

  <div id="isCompany" v-if="shape">

    <q-input filled v-model="entity.cpf_cnpj" mask="##.###.###/####-##" label="CPF/CNPJ" style="margin: 2%;" />
    <q-input filled v-model="entity.corporate_name" label="Razão social" maxlength = "45" style="margin: 2%;" />
    <q-input filled v-model="entity.fantasy_name" maxlength = "40" label="Nome fantasia" style="margin: 2%;" />
    <q-input filled v-model="entity.cep" v-on:keyup="buscaCEP" label="CEP" style="margin: 2%;" />
    <q-input filled v-model="entity.address" label="Endereço" style="margin: 2%;" />
    <q-input filled v-model="entity.city" label="Cidade" maxlength = "45" style="margin: 2%;" />
    <q-input filled v-model="entity.state" label="Estado" maxlength = "2" style="margin: 2%;" />
    <q-input filled v-model="entity.phone" v-mask="'(##) ####-#####'" label="Celular" style="margin: 2%;" />
    <q-input filled v-model="entity.email" label="E-mail" style="margin: 2%;" />

  </div>


  <div style="margin: 2%;">
    <q-btn label="Criar entidade" v-on:click=create color="primary"/>
    <q-btn label="Limpar campos" type="reset" color="primary" flat class="q-ml-sm" />
  </div>  

</div>
</template>

<style>
</style>

<script>

import { ref } from 'vue'
import axios from "axios"
import {mask} from 'vue-the-mask'

export default {
   directives: {mask},
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
    },

    async buscaCEP(){
        var config = {
        method: 'get',
        url: `https://viacep.com.br/ws/${this.entity.cep}/json/`,
        headers: { }
      };

      await axios(config)
      .then((response) => {
        console.log(JSON.stringify(response.data));
        this.entity.address = response.data.logradouro
        this.entity.state = response.data.uf
        this.entity.city = response.data.localidade
      })
      .catch((error) => {
        console.log(error);
      });
    }
  }
}
</script>
