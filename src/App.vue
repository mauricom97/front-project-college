<template>
<q-layout style="max-width: 100%;" >

<login v-if="logado == false" @loginUser="getLogin"></login>

<div v-if="logado == true">

    <q-header elevated class="text-white" style="background: #24292e" height-hint="61.59">
      <q-toolbar class="q-py-sm q-px-md">
        <q-btn round dense flat :ripple="false" :icon="fabGithub" size="19px" color="white" class="q-mr-sm" no-caps />

        <q-select
          ref="search" dark dense standout use-input hide-selected
          class="GL__toolbar-select"
          color="black" :stack-label="false" label="Pesquisar"
          v-model="text" :options="filteredOptions" @filter="filter"
          style="width: 300px"
        >

          <template v-slot:append>
            <img src="https://cdn.quasar.dev/img/layout-gallery/img-github-search-key-slash.svg">
          </template>

          <template v-slot:no-option>
            <q-item>
              <q-item-section>
                <div class="text-center">
                  <q-spinner-pie
                    color="grey-5"
                    size="24px"
                  />
                </div>
              </q-item-section>
            </q-item>
          </template>

          <template v-slot:option="scope">
            <q-item
              v-bind="scope.itemProps"
              class="GL__select-GL__menu-link"
            >
              <q-item-section side>
                <q-icon name="collections_bookmark" />
              </q-item-section>
              <q-item-section>
                <q-item-label v-html="scope.opt.label" />
              </q-item-section>
              <q-item-section side :class="{ 'default-type': !scope.opt.type }">
                <q-btn outline dense no-caps text-color="blue-grey-5" size="12px" class="bg-grey-1 q-px-sm">
                  {{ scope.opt.type || 'Jump to' }}
                  <q-icon name="subdirectory_arrow_left" size="14px" />
                </q-btn>
              </q-item-section>
            </q-item>
          </template>
        </q-select>

        <div v-if="$q.screen.gt.sm" class="GL__toolbar-link q-ml-xs q-gutter-md text-body2 text-weight-bold row items-center no-wrap">
          <a href="javascript:void(0)" v-on:click="fun_searchEntity()" class="text-white">
            Listar Entidades
          </a>
        </div>

        <q-space />

        <div class="q-pl-sm q-gutter-sm row items-center no-wrap">
          <q-btn v-if="$q.screen.gt.xs" dense flat>
            <div class="row items-center no-wrap">
              <q-icon name="add" size="20px" />
              <q-icon name="arrow_drop_down" size="16px" style="margin-left: -2px" />
            </div>
            <q-menu auto-close>
              <q-list dense style="min-width: 100px">
                <q-item clickable class="GL__menu-link">
                  <q-item-section v-on:click="fun_createEntity()">Nova Entidade</q-item-section>
                </q-item>
              </q-list>
            </q-menu>
          </q-btn>

          <q-btn dense flat no-wrap>
            <q-avatar rounded size="20px">
              <img src="https://cdn.quasar.dev/img/avatar3.jpg">
            </q-avatar>
            <q-icon name="arrow_drop_down" size="16px" />

            <q-menu auto-close>
              <q-list dense>
                <q-item class="GL__menu-link-signed-in">
                  <q-item-section>
                    <div>Olá, <strong>usuário</strong></div>
                  </q-item-section>
                </q-item>
                <q-separator />
                
                <q-separator />
                <q-item clickable class="GL__menu-link">
                  <q-item-section>Seu Perfil</q-item-section>
                </q-item>
                <q-item clickable class="GL__menu-link">
                  <q-item-section>Configuração</q-item-section>
                </q-item>
                <q-item clickable class="GL__menu-link">
                  <q-item-section v-on:click="sair()" >Sair</q-item-section>
                </q-item>
              </q-list>
            </q-menu>
          </q-btn>
        </div>
      </q-toolbar>
    </q-header>
    
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

    <createEntity v-if="comp_createEntity == true" :dataEditEntity="dataEditEntity" class="absolute-center q-mt-xl" style="max-width: 40%; margin-top: 15%;"/>
    <searchEntity v-if="comp_searchEntity == true" :entidade="'teste'" style="position: absolute; top: 20%; right:15% " @editEntity="getData"/>
  </div>

</div>

</q-layout>
</template>

<script>
import { ref } from 'vue'
import createEntity from './components/createEntity.vue'
import searchEntity from './components/searchEntity.vue'
import login from './components/login.vue'
export default {
  name: 'LayoutDefault',

  components: {
    createEntity,
    searchEntity,
    login
  },

  data(){
    return{
      comp_createEntity: false,
      comp_searchEntity: false,
      dataEditEntity: null,
      logado: false
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
    },
    getLogin(value) {
      console.log(value)
      if(value.response == "true"){
        this.logado = true
      }
    },
    sair(){
      this.logado = false
    }
  },

  setup () {
    return {
      leftDrawerOpen: ref(false)
    }
  }
}
</script>

<style lang="sass">
.GL
  &__select-GL__menu-link
    .default-type
      visibility: hidden
    &:hover
      background: #0366d6
      color: white
      .q-item__section--side
        color: white
      .default-type
        visibility: visible
  &__toolbar-link
    a
      color: white
      text-decoration: none
      &:hover
        opacity: 0.7
  &__menu-link:hover
    background: #0366d6
    color: white
  &__menu-link-signed-in,
  &__menu-link-status
    &:hover
      & > div
        background: white !important
  &__menu-link-status
    color: grey
    &:hover
      color: blue
  &__toolbar-select.q-field--focused
    width: 450px !important
    .q-field__append
      display: none
</style>
