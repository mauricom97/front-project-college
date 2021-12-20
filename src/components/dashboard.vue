<template>
  <q-page>
      <div class="row flex-center" style="min-height: 60vh;">
        <div class="col-4">
          <q-card>
            <q-card-section>
              <div class="clients">
                <div class="text-h4" style="text-align: center; margin-bottom: 15%; ">
                  Dashboard
                </div>

                <label>{{clients.qtd}} de 3 clientes cadastrados</label>
                <q-linear-progress
                  size="50px"
                  :value="progress"
                  color="accent"
                  class="q-mt-sm"
                >
                  <div class="absolute-full flex flex-center">
                    <q-badge
                      color="white"
                      text-color="accent"
                      :label="clients.percent"
                    />
                  </div>
                </q-linear-progress>
              </div>
            </q-card-section>
          </q-card>
        </div>
      </div>
  </q-page>
</template>

<script>
import { ref, onMounted, onBeforeUnmount } from "vue";
import axios from "axios";
export default {
  data() {
    return {
      clients: { percent: "0%" },
    };
  },
  setup() {
    const progress = ref(0.0);
    const buffer = ref(0.01);

    let interval, bufferInterval;

    onMounted(() => {
      interval = setInterval(() => {
        if (progress.value >= 1) {
          progress.value = 0.01;
          buffer.value = 0.01;
          return;
        }
      }, 700 + Math.random() * 1000);
      bufferInterval = setInterval(() => {
        if (buffer.value < 1) {
          buffer.value = Math.min(1, buffer.value + Math.random() * 0.2);
        }
      }, 700);
    });

    onBeforeUnmount(() => {
      clearInterval(interval);
      clearInterval(bufferInterval);
    });

    return {
      progress,
      buffer,
    };
  },
  methods: {
    getEntities: async function () {
      var config = {
        method: "get",
        url: "http://localhost:3352/entities",
        headers: {},
      };

      await axios(config)
        .then((response) => {
          console.log(response.data.response.success);
          let entities = response.data.response.success;
          this.clients.qtd = response.data.response.success.length
          if (entities.length == 0) {
            this.progress = 0.0;
            this.clients.percent = "0%";
          } else if (entities.length == 1) {
            this.progress = 0.1;
            this.clients.percent = "33,5%";
          } else if (entities.length == 2) {
            this.progress = 0.5;
            this.clients.percent = "50%";
          } else if (entities.length == 3) {
            this.progress = 1;
            this.clients.percent = "100%";
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
  beforeMount() {
    this.getEntities();
  },
};
</script>

<style></style>
