<template>
  <div class="col-5">
    <div class="users row" style="width: 100%">
      <div style="margin: 5%">
        <div v-for="user in users_actives" :key="user.user_uuid">
          <q-card class="q-ml-md" style="margin: 5%">
            <q-card-section>
              <div class="text-h6">{{ user.user_name }}</div>
              <div class="text-subtitle2 ellipsis">{{ user.user_email }}</div>
              <q-chip square>
                <q-avatar icon="bookmark" color="green" text-color="white" />
                Usuário ativo
              </q-chip>
            </q-card-section>
            <q-separator />

            <q-card-actions align="right">
              <q-btn flat>Action 1</q-btn>
              <q-btn flat>Action 2</q-btn>
            </q-card-actions>
          </q-card>
        </div>
      </div>
      <div style="margin: 5%">
        <div v-for="user in users_not_actives" :key="user.user_uuid">
          <q-card style="margin: 2%">
            <q-card-section>
              <div class="text-h6">{{ user.user_name }}</div>
              <div class="text-subtitle2 ellipsis">{{ user.user_email }}</div>
              <q-chip square>
                <q-avatar icon="bookmark" color="red" text-color="white" />
                Usuário inativo
              </q-chip>
            </q-card-section>
            <q-separator />

            <q-card-actions align="right">
              <q-btn flat v-on:click="ativarUsuario(user.user_uuid)"
                >Ativar usuario</q-btn
              >
              <q-btn flat v-on:click="deleteUser(user.user_uuid)">Deletar solicitação</q-btn>
            </q-card-actions>
          </q-card>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      users: null,
      users_not_actives: [],
      users_actives: [],
    };
  },
  methods: {
    getUsers: async function () {
      var config = {
        method: "get",
        url: `http://localhost:3352/users`,
        headers: {},
      };
      await axios(config)
        .then((response) => {
          console.log(JSON.stringify(response.data));
          if (
            this.users_not_actives.length > 0 ||
            this.users_actives.length > 0
          ) {
            this.users_not_actives = [];
            this.users_actives = [];
          }
          this.users = response.data.response.success;
          for (let user of this.users) {
            if (user.active == false) {
              this.users_not_actives.push(user);
            } else if (user.active == true) {
              this.users_actives.push(user);
            }
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
    ativarUsuario: async function (uuid) {
      var data = JSON.stringify({
        active: true,
      });

      var config = {
        method: "put",
        url: `http://localhost:3352/users/active/${uuid}`,
        headers: {
          "Content-Type": "application/json",
        },
        data: data,
      };

      await axios(config)
        .then((response) => {
          console.log(JSON.stringify(response.data));
          this.getUsers();
        })
        .catch((error) => {
          console.log(error);
        });
    },

    deleteUser: async function (uuid) {
      var config = {
        method: "delete",
        url: `http://localhost:3352/users/${uuid}`,
        headers: {},
      };

      await axios(config)
        .then((response) => {
          console.log(JSON.stringify(response.data));
          this.getUsers()
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
  beforeMount() {
    this.getUsers();
  },
};
</script>

<style>
.users {
  position: absolute;
  top: 15%;
  max-width: 50%;
}
</style>
