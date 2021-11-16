<template>
<div>
  <Entity style="position:absolute;"/>
</div>
</template>

<script>
import { ref } from 'vue'
import Entity from './components/Entity.vue'

export default {
  name: 'LayoutDefault',

  components: {
    Entity
  },

  setup () {
    return {
      leftDrawerOpen: ref(false)
    }
  }
}
</script>
