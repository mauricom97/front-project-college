<template>
<q-layout>

  <div id="cadastroEntidade" style="margin: 15%; top:5%;">

    <createEntity style="position:absolute; left: 30%;"/>
    <searchEntity />
  </div>

</q-layout>
</template>

<script>
import { ref } from 'vue'
import createEntity from './components/createEntity.vue'
//import searchEntity from './components/searchEntity.vue'
export default {
  name: 'LayoutDefault',

  components: {
    createEntity
    //searchEntity
  },

  setup () {
    return {
      leftDrawerOpen: ref(false)
    }
  }
}
</script>
