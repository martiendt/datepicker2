<script>
import { defineComponent } from 'vue';
import VuePicker from '@/vue-picker.vue';

export default defineComponent({
  name: 'ServeDev',
  components: {
    VuePicker
  }
});
</script>

<template>
  <div id="app">
    <vue-picker />
  </div>
</template>
