<template>
  <div>
      <q-input style="position:relative; top:5%;" v-model="search" filled type="search" hint="Buscar entidade">
        <template v-slot:append>
          <q-icon name="search" />
        </template>
      </q-input>

      <div id="listEntity" style="position:absolute;">
          <li v-for="entity in listEntity" :key="entity.uuid">
            <q-card class="my-card">
              <q-card-section>
                Nome: {{entity.fantasy_name}}
                <q-btn round class="q-mr-sm q-ml-sm" color="red" icon="delete" v-on:click="dialogEntity(entity.uuid, entity.fantasy_name, 'delete')" />
                <q-btn round class="q-mr-sm q-ml-sm" color="primary" icon="edit" v-on:click="dialogEntity(entity.uuid, entity.fantasy_name, 'edit')" />
              </q-card-section>
            </q-card>
        </li>
      </div>

    <q-dialog v-model="confirmDelete" persistent>
      <q-card>
        <q-card-section class="row items-center">
          <q-icon name="warning" class="text-red" style="font-size: 4rem;" />
          <span class="q-ml-sm">Tem certeza que deseja deletar {{nameEntity}}</span>
        </q-card-section>

        <q-card-actions align="right">
          <q-btn flat label="Cancelar" color="primary" v-close-popup />
          <q-btn flat label="Deletar" color="red" v-on:click="deleteEntity(uuidEntity)" v-close-popup />
        </q-card-actions>
      </q-card>
    </q-dialog>

  </div>
</template>

<script>

import axios from "axios";

export default {
  data(){
    return{
      listEntity: null,
      confirmDelete: false,
      uuidEntity: null,
      nameEntity: null
    }
  },
  methods:{
    getEntities: async function() {
        var config = {
        method: "get",
        url: `http://localhost:3352/entities`,
        headers: {},
      };

        await axios(config)
        .then((response) => {
          console.log(JSON.stringify(response.data));
          this.listEntity = response.data.response.success
          return this.listEntity
        })
        .catch((error) => {
          console.log(error);
        });
    },

    dialogEntity: async function (uuid, name, type) {
      if(type === "delete"){
        this.confirmDelete = true
      }else if(type === "edit"){
        this.confirmEdit = true
      }
      this.uuidEntity = uuid
      this.nameEntity = name
    },

    deleteEntity: async function (uuid){
      this.confirm = true
      var config = {
        method: 'delete',
        url: `http://localhost:3352/entities/${uuid}`,
        headers: { }
      };

      await axios(config)
      .then((response) => {
        console.log(JSON.stringify(response.data));

      Object.prototype.removeItem = function (key, value) {
          if (value == undefined)
              return;

          for (var i in this) {
              if (this[i][key] == value) {
                  this.splice(i, 1);
              }
          }
      };

      this.listEntity.removeItem("uuid", uuid)

      })
      .catch(function (error) {
        console.log(error);
      });

    }
 },
 beforeMount(){
    this.getEntities()
 },
}
</script>

<style>

</style>