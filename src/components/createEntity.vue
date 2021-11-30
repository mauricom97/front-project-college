<template>
  <div class="absolute-center">
    <div id="isCompany" v-if="shape">
      <div class="row">
        <div class="col">
          <div class="text-h4">Cadastro de entidades</div>
          <q-toggle v-model="pessoafisica" label="Pessoa fisica" />
          <q-input
            filled
            v-model="entity.cpf_cnpj"
            mask="##.###.###/####-##"
            v-if="pessoafisica == false"
            label="CNPJ"
            style="margin: 2%"
            v-on:keyup="getEntityEdit()"
          />
          <q-input
            filled
            v-model="entity.cpf_cnpj"
            mask="###.###.###-##"
            v-if="pessoafisica != false"
            label="CPF"
            style="margin: 2%"
            v-on:keyup="getEntityEdit()"
          />

          <q-input
            filled
            v-model="entity.corporate_name"
            v-if="pessoafisica == false"
            label="Razão social"
            maxlength="45"
            style="margin: 2%"
          />
          <q-input
            filled
            v-model="entity.fantasy_name"
            maxlength="40"
            label="Nome fantasia"
            style="margin: 2%"
          />

          <q-input
            filled
            v-model="entity.cep"
            v-on:keyup="buscaCEP"
            label="CEP"
            style="margin: 2%"
          />
          <q-input
            filled
            v-model="entity.address"
            label="Endereço"
            style="margin: 2%"
          />
          <q-input
            filled
            v-model="entity.city"
            label="Cidade"
            maxlength="45"
            style="margin: 2%"
          />
          <q-input
            filled
            v-model="entity.state"
            label="Estado"
            maxlength="2"
            style="margin: 2%"
          />
          <q-input
            filled
            v-model="entity.phone"
            v-mask="'(##) ####-#####'"
            label="Celular"
            style="margin: 2%"
          />
          <q-input
            filled
            v-model="entity.email"
            label="E-mail"
            style="margin: 2%"
          />
        </div>
      </div>
    </div>

    <q-chat-message
      v-if="message != null"
      name="Aviso"
      avatar="https://cdn.quasar.dev/img/avatar4.jpg"
      :text="[message]"
      sent
      stamp="Agora"
    />

    <div style="margin: 2%; text-align: center">
      <div class="q-pa-md">
        <div class="q-gutter-sm">
          <q-checkbox label="Cliente" v-model="client" />
          <q-checkbox label="Fornecedor" v-model="supplier" />
          <q-checkbox label="Agencia" v-model="agency" />
        </div>

        <div class="q-px-sm"></div>
      </div>

      <q-btn label="Criar entidade" v-if="!updateEntity" v-on:click="create" color="orange-6" />
      <q-btn label="Atualizar entidade" v-on:click="update" v-if="updateEntity" color="orange-6" />
      <q-btn
        label="Limpar campos"
        v-on:click="limparDados()"
        color="primary"
        flat
        class="q-ml-sm"
      />
    </div>

    <div class="q-pa-md row justify-center">
      <div style="width: 100%; max-width: 400px">
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import axios from "axios";
import { mask } from "vue-the-mask";

export default {

  props:{
    dataEditEntity: Object
  },

  directives: { mask },
  setup() {
    return {
      shape: ref("false"),
      client: ref("false"),
      agency: ref("false"),
      supplier: ref("false"),
    };
  },
  data() {
    return {
      entity: {
        company: null,
        fantasy_name: null,
        corporate_name: null,
        cep: null,
        address: null,
        state: null,
        city: null,
        cpf_cnpj: null,
        phone: null,
        email: null,
        type: {
          client: false,
          agency: false,
          supplier: false
        },
      },
      pessoafisica: false,
      message: null,
      updateEntity: null,
      dataEntityUuid: null
    };
  },

  methods: {
    async create() {
      this.entity.type.client = this.client;
      this.entity.type.agency = this.agency;
      this.entity.type.supplier = this.supplier;
      this.entity.company = this.pessoafisica
      if (this.pessoafisica) {
        this.entity.corporate_name = this.fantasy_name;
      }
      this.entity.company = this.shape;
      await axios
        .post("http://localhost:3352/entities", this.entity)
        .then((response) => {
          console.log(response);
          if(response.data.response.success){
            this.message = "Entidade criada com sucesso"
            this.clear_object(this.entity);
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },

    async update() {

        var config = {
        method: 'put',
        url: `http://localhost:3352/entities/${this.dataEntityUuid}`,
        headers: { 
          'Content-Type': 'application/json'
        },
        data : this.entity
      };

      await axios(config)
      .then(function (response) {
        console.log(JSON.stringify(response.data));
      })
      .catch(function (error) {
        console.log(error);
      });

    },

    async getDataEntity() {
        if(this.dataEditEntity != null){

        console.log(this.dataEditEntity)

        
        if(this.dataEditEntity.uuid){
          this.dataEntityUuid = this.dataEditEntity.uuid
        }else{
          this.dataEntityUuid = this.entity.uuid
        }

        var config = {
          method: 'get',
          url: `http://localhost:3352/entities/${this.dataEntityUuid}`,
          headers: { }
        };

        await axios(config)
        .then( (response) => {
          console.log(JSON.stringify(response.data));
            this.entity = response.data.response.success
            this.pessoafisica = !this.entity.company
        })
        .catch(function (error) {
          console.log(error);
        });
      }
    },

    async getEntityEdit() {
      if(this.entity.cpf_cnpj.length === 14 && this.pessoafisica == true || this.entity.cpf_cnpj.length === 18 && this.pessoafisica == false)
      var config = {
        method: 'get',
        url: `http://localhost:3352/entities/cgcic/${this.entity.cpf_cnpj}`,
        headers: { }
      };

      await axios(config)
      .then((response) => {
        console.log(JSON.stringify(response.data));
        if(response.data.response.success != null || response.data.response.success){
          this.entity = response.data.response.success
          this.dataEntityUuid = response.data.response.success.uuid
          console.log(this.entity)
          this.pessoafisica = !response.data.response.company
          this.updateEntity = true
          
        }
      })
      .catch((error) => {
        console.log(error);
      });
    },

    limparDados() {
      this.entity = this.clear_object(this.entity);
    },

    clear_object(obj) {
      for (const key in obj) {
        obj[key] =
          typeof obj[key] === "object"
            ? Array.isArray(obj[key])
              ? []
              : this.clear_object(obj[key])
            : null;
      }
      return obj;
    },

    async buscaCEP() {
      var config = {
        method: "get",
        url: `https://viacep.com.br/ws/${this.entity.cep}/json/`,
        headers: {},
      };

      await axios(config)
        .then((response) => {
          console.log(JSON.stringify(response.data));
          this.entity.address = response.data.logradouro;
          this.entity.state = response.data.uf;
          this.entity.city = response.data.localidade;
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
  mounted () {

  },
  beforeMount(){
    if(this.dataEditEntity){
      this.updateEntity = true
      console.log(this.getDataEntity())
    }
 }
};
</script>
