<template>
  <div class="map">
    <component :is="currentView"></component>
  </div>
</template>

<script>
import AVURNAVs from './components/AVURNAVs';

export default {
  name: 'app',
  components: {
    AVURNAVs
  },
  data () {
    return {
      currentView: AVURNAVs
    }
  }
}
</script>

<style>
@import "../node_modules/leaflet/dist/leaflet.css";

body {
  padding: 0;
  margin: 0;
}
html, body, .map {
  height: 100%;
  width: 100%;
}
</style>
