<script>
import { defineComponent } from 'vue';
import VueIdentitypass from '@/vue-identitypass.vue';

export default defineComponent({
  name: 'ServeDev',
  components: {
    VueIdentitypass
  }
});
</script>

<template>
  <div id="app">
    <vue-identitypass />
  </div>
</template>
