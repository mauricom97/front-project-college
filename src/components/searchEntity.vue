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
              </q-card-section>
            </q-card>
        </li>
      </div>
  </div>
</template>

<script>

import axios from "axios";

export default {
  data(){
    return{
      listEntity: null
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
        })
        .catch((error) => {
          console.log(error);
        });
    }
 },
 beforeMount(){
    this.getEntities()
 }
}
</script>

<style>

</style>