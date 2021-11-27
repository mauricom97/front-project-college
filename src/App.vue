<template>
<q-layout style="position:absolute; top: 5%;" >

      <q-btn style="position:absolute; right:5%; top:1%;" color="primary" label="Entidades">
        <q-menu
          transition-show="flip-right"
          transition-hide="flip-left"
        >
          <q-list style="min-width: 100px">
            <q-item clickable>
              <q-item-section v-on:click="fun_createEntity()">Cadastro de entidades</q-item-section>
            </q-item>
            <q-item clickable>
              <q-item-section v-on:click="fun_searchEntity()" >Lista de entidades</q-item-section>
            </q-item>
            <q-separator />
            <q-item clickable>
              <q-item-section>Mind blown</q-item-section>
            </q-item>
          </q-list>
        </q-menu>
      </q-btn>


  <div id="cadastroEntidade" style="margin: 15%; top:5%;">

    <createEntity v-if="comp_createEntity == true" :dataEditEntity="dataEditEntity" class="absolute-center q-mt-xl"/>
    <searchEntity v-if="comp_searchEntity == true" :entidade="'teste'" style="position: absolute; top: 1%; right:15% " @editEntity="getData"/>
  </div>

</q-layout>
</template>

<script>
import { ref } from 'vue'
import createEntity from './components/createEntity.vue'
import searchEntity from './components/searchEntity.vue'
export default {
  name: 'LayoutDefault',

  components: {
    createEntity,
    searchEntity
  },

  data(){
    return{
      comp_createEntity: false,
      comp_searchEntity: false,
      dataEditEntity: null
    }
  },

  methods: {
    fun_createEntity() {
      this.comp_createEntity = true
      this.comp_searchEntity = false
    },
    fun_searchEntity() {
      this.comp_createEntity = false
      this.comp_searchEntity = true
    },
    getData(value) {
      if(value.type == 'edit'){
        this.comp_createEntity = true
        this.comp_searchEntity = false
        this.dataEditEntity = value
      }
    }
  },

  setup () {
    return {
      leftDrawerOpen: ref(false)
    }
  }
}
</script>
