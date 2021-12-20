<template>
  <div class="absolute-center">
    <q-input
      style="position: relative; top: 15%"
      v-on:keyup="getEntityName()"
      v-model="search"
      filled
      type="search"
      hint="Buscar entidade"
    >
      <template v-slot:append>
        <q-icon name="search" />
      </template>
    </q-input>

    <div class="container">
      <div class="col">
        <div class="row">
          <div id="listEntity" style="position: absolute">
            <div
              class="q-pa-md row items-start q-gutter-md"
              style="width: 1120px; margin-left: -12%"
            >
              <div
                v-for="entity in listEntity"
                :key="entity.uuid"
                class="col-3 bg-orange-7"
              >
                <q-card class="my-card">
                  <q-img
                    src="https://www.pngitem.com/pimgs/m/74-742680_company-png-transparent-background-white-company-icon-png.png"
                  >
                    <div class="absolute-bottom">
                      <div class="text-h6">{{ entity.fantasy_name }}</div>
                    </div>
                  </q-img>
                  <q-card-actions>
                    <q-btn
                      flat
                      icon="edit"
                      color="primary"
                      v-on:click="
                        editEntity(entity.uuid, entity.fantasy_name, 'edit')
                      "
                      >Editar</q-btn
                    >
                    <q-btn
                      flat
                      icon="delete"
                      color="red"
                      v-on:click="
                        dialogEntity(entity.uuid, entity.fantasy_name, 'delete')
                      "
                      >Deletar</q-btn
                    >
                  </q-card-actions>
                </q-card>
              </div>
            </div>
          </div>

          <q-dialog v-model="confirmDelete" persistent>
            <q-card>
              <q-card-section class="row items-center">
                <q-icon
                  name="warning"
                  class="text-red"
                  style="font-size: 4rem"
                />
                <span class="q-ml-sm"
                  >Tem certeza que deseja deletar {{ nameEntity }}</span
                >
              </q-card-section>

              <q-card-actions align="right">
                <q-btn flat label="Cancelar" color="primary" v-close-popup />
                <q-btn
                  flat
                  label="Deletar"
                  color="red"
                  v-on:click="deleteEntity(uuidEntity)"
                  v-close-popup
                />
              </q-card-actions>
            </q-card>
          </q-dialog>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  props: {
    entidade: String,
  },

  data() {
    return {
      listEntity: null,
      confirmDelete: false,
      uuidEntity: null,
      nameEntity: null,
      search: null,
    };
  },
  methods: {
    getEntities: async function () {
      var config = {
        method: "get",
        url: `http://localhost:3352/entities`,
        headers: {},
      };
      await axios(config)
        .then((response) => {
          console.log(JSON.stringify(response.data));
          this.listEntity = response.data.response.success;
          return this.listEntity;
        })
        .catch((error) => {
          console.log(error);
        });
    },
    dialogEntity: async function (uuid, name, type) {
      console.log(type);
      if (type === "delete") {
        this.confirmDelete = true;
      } else if (type === "edit") {
        this.confirmEdit = true;
      }
      this.uuidEntity = uuid;
      this.nameEntity = name;
    },
    deleteEntity: async function (uuid) {
      this.confirm = true;
      var config = {
        method: "delete",
        url: `http://localhost:3352/entities/${uuid}`,
        headers: {},
      };
      await axios(config)
        .then((response) => {
          console.log(JSON.stringify(response.data));
          Object.prototype.removeItem = function (key, value) {
            if (value == undefined) return;
            for (var i in this) {
              if (this[i][key] == value) {
                this.splice(i, 1);
              }
            }
          };
          this.listEntity.removeItem("uuid", uuid);
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    getEntityName: async function () {
      var config = {
        method: "get",
        url: `http://localhost:3352/entities/name/${this.search}`,
        headers: {},
      };

      await axios(config)
        .then((response) => {
          console.log(JSON.stringify(response.data));
          let entity = [];
          entity.push(response.data.response.success);
          this.listEntity = entity;
        })
        .catch((error) => {
          console.log(error);
        });
      if (this.search == "") {
        this.getEntities();
      }
    },
    editEntity: async function (uuid, name, type) {
      this.$emit("editEntity", { uuid, name, type });
    },
  },
  beforeMount() {
    this.getEntities();
  },

  mounted() {
    console.log(this.entidades);
  },
};
</script>

<style>
.my-card {
  width: 100%;
  max-width: 280px;
}
</style>
