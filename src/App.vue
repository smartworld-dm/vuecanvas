<template>
  <div id="app">
    <dashboard></dashboard>
  </div>
</template>

<script>
import MyCanvasPixi from './components/CanvasPixi.vue';
import Dashboard from './components/Dashboard.vue';

export default {
  name: 'app',
  components: {
    MyCanvasPixi,
    Dashboard,
  },

  data () {
    return {
    }
  },
  mounted () {
  }
}
</script>
