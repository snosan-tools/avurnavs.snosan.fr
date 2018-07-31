<template>
  <div class="map">
    <l-map :zoom="zoom" :center="center">
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <l-marker v-for="marker in markers" :key="marker.id"
        :visible="marker.visible"
        :lat-lng="marker.position">
        <l-popup :content="marker.tooltip" />
      </l-marker>
    </l-map>
  </div>
</template>

<script>
import { LMap, LTileLayer, LMarker, LPopup } from 'vue2-leaflet';
import axios from 'axios';

export default {
  name: 'example',
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LPopup
  },
  data () {
    return {
      show: true,
      zoom: 6,
      center:[48, -1.219482],
      url:'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution:'&copy; <a href="http://osm.org/copyright">OpenStreetMap</a>',
      markers: [],
    }
  },
  methods: {
    cleanDate: function(date) {
      if (date === null) {
        return 'Inconnue'
      }
      return date
    }
  },
  created() {
    ["atlantique", "manche", "méditerranée"].forEach(region => {
      axios.get(`https://avurnav.antoine-augusti.fr/avurnavs/regions/${region}`).then(response => {
        response.data.forEach(el => {
          this.markers.push({
            position: {
              lng: el['longitude'],
              lat: el['latitude']
            },
            visible: true,
            tooltip: `
              <b>${el['title']}</b>
              <br/><br/>
              ${el['content']}
              <br/><br/>
              Plus d'informations : <a href="${el['url']}">site de la préfecture maritime</a>.
              <br/><br/>
              Date de début : ${this.cleanDate(el['valid_from'])}<br/>
              Date de fin : ${this.cleanDate(el['valid_until'])}<br/>
              `
          })
        })
    })
    })
  },
}
</script>
