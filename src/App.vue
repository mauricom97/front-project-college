<template>
  <q-layout class="bg-orange-7">
    <login v-if="!logado" @loginUser="getLogin"></login>

    <div v-if="logado">
      <q-header
        elevated
        class="text-white"
        style="background: #24292e; position: fixed; top: 0px"
        height-hint="61.59"
      >
        <q-toolbar class="q-py-sm q-px-md">
          <q-btn
            round
            dense
            flat
            :ripple="false"
            :icon="fabGithub"
            size="19px"
            color="white"
            class="q-mr-sm"
            no-caps
          />

          <div
            v-if="$q.screen.gt.sm"
            class="
              GL__toolbar-link
              q-ml-xs q-gutter-md
              text-body2 text-weight-bold
              row
              items-center
              no-wrap
            "
          >
            <q-btn color="orange-7" label="Inicio" v-on:click="fun_home()" />

            <!-- ENTIDADES -->

            <q-btn-dropdown color="orange-7" label="ENTIDADES">
              <q-list>
                <q-item clickable v-close-popup @click="onItemClick">
                  <q-item-section>
                    <q-item-label v-on:click="fun_createEntity()"
                      >Criar entidade</q-item-label
                    >
                  </q-item-section>
                </q-item>

                <q-item clickable v-close-popup @click="onItemClick">
                  <q-item-section>
                    <q-item-label v-on:click="fun_searchEntity()"
                      >Lista de entidades</q-item-label
                    >
                  </q-item-section>
                </q-item>
              </q-list>
            </q-btn-dropdown>
          </div>

          <q-space />

          <div class="q-pl-sm q-gutter-sm row items-center no-wrap">
            <q-btn dense flat no-wrap>
              <q-avatar rounded size="50px">
                <img :src="infUser.img_perfil" />
              </q-avatar>
              <q-icon name="arrow_drop_down" size="16px" />

              <q-menu auto-close>
                <q-list dense>
                  <q-item class="GL__menu-link-signed-in">
                    <q-item-section>
                      <div>
                        Olá, <strong>{{ infUser.user_name }}</strong>
                      </div>
                    </q-item-section>
                  </q-item>
                  <q-separator />

                  <q-separator />
                  <q-item clickable class="GL__menu-link">
                    <q-item-section>Seu Perfil</q-item-section>
                  </q-item>
                  <q-item clickable class="GL__menu-link">
                    <q-item-section v-on:click="fun_users()"
                      >Usuários</q-item-section
                    >
                  </q-item>
                  <q-item clickable class="GL__menu-link">
                    <q-item-section v-on:click="sair()">Sair</q-item-section>
                  </q-item>
                </q-list>
              </q-menu>
            </q-btn>
          </div>
        </q-toolbar>
      </q-header>

      <q-page-container>
        <div id="cadastroEntidade">
          <createEntity
            v-if="comp_createEntity == true"
            :dataEditEntity="dataEditEntity"
            class="q-pa-md"
          />
          <searchEntity
            v-if="comp_searchEntity == true"
            :entidade="'teste'"
            style="position: absolute; top: 20%; right: 15%; max-width: 35%"
            @editEntity="getData"
          />
          <users v-if="comp_users"></users>
          <dashboard v-if="comp_dashboard"></dashboard>
        </div>
      </q-page-container>
    </div>
  </q-layout>
</template>

<script>
import { ref } from "vue";
import createEntity from "./components/createEntity.vue";
import searchEntity from "./components/searchEntity.vue";
import login from "./components/login.vue";
import users from "./components/users.vue";
import dashboard from "./components/dashboard.vue";
import axios from "axios";

export default {
  name: "LayoutDefault",

  components: {
    createEntity,
    searchEntity,
    login,
    users,
    dashboard
  },

  data() {
    return {
      comp_dashboard: true,
      comp_createEntity: false,
      comp_searchEntity: false,
      comp_users: false,
      dataEditEntity: null,
      logado: false,
      infUser: null,
    };
  },

  methods: {
    fun_home() {
      this.comp_createEntity = false;
      this.comp_searchEntity = false;
      this.comp_users = false;
      this.comp_dashboard = true
    },
    fun_createEntity() {
      this.comp_createEntity = true;
      this.comp_searchEntity = false;
      this.comp_users = false;
      this.comp_dashboard = false
    },
    fun_searchEntity() {
      this.comp_createEntity = false;
      this.comp_searchEntity = true;
      this.comp_users = false;
      this.comp_dashboard = false
    },
    fun_users() {
      this.comp_createEntity = false;
      this.comp_searchEntity = false;
      this.comp_users = true;
      this.comp_dashboard = false
    },
    getData(value) {
      if (value.type == "edit") {
        this.comp_createEntity = true;
        this.comp_searchEntity = false;
        this.dataEditEntity = value;
      }
    },
    getLogin(value) {
      console.log(value);
      if (value.response == "true") {
        localStorage.setItem(
          "session_token_corp",
          value.data.session.session_token
        );
        this.infUser = value.data;
        if(!this.infUser.img_perfil){
            this.infUser.img_perfil = "https://cdn.pixabay.com/photo/2019/08/11/18/59/icon-4399701_1280.png"
        }
        console.log(this.infUser)
        this.logado = true;
        console.log(this.infUser);
      }
    },
    async sair() {
      var data = JSON.stringify({
        session_token: localStorage.session_token_corp,
      });

      var config = {
        method: "delete",
        url: "http://localhost:3352/session",
        headers: {
          "Content-Type": "application/json",
        },
        data: data,
      };

      await axios(config)
        .then((response) => {
          console.log(JSON.stringify(response.data));
          localStorage.removeItem("session_token_corp");
        })
        .catch((error) => {
          console.log(error);
        });

      this.comp_createEntity = false;
      this.comp_searchEntity = false;
      this.comp_users = false;
      this.logado = false;
    },
    async verifySession() {
      console.log(localStorage.session_token_corp);
      var data = JSON.stringify({
        session_token: localStorage.session_token_corp,
      });

      var config = {
        method: "post",
        url: "http://localhost:3352/session",
        headers: {
          "Content-Type": "application/json",
        },
        data: data,
      };

      await axios(config)
        .then((response) => {
          this.infUser = response.data.response.success.User;
          this.logado = true;
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
  beforeMount() {
    this.verifySession();
  },
  mounted() {
    console.log(this.entidades);
  },

  setup() {
    return {
      leftDrawerOpen: ref(false),
    };
  },
};
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
