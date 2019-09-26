<template>
  <div class="map">
    <l-map :zoom="zoom" :center="center">
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <l-tile-layer :url="seaUrl" :attribution="seaAttribution"></l-tile-layer>
      <l-marker v-for="marker in markers" :key="marker.id"
        :visible="marker.visible"
        :icon="marker.icon"
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
      markers: [],
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a>',
      seaUrl: 'https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png',
      seaAttribution: '&copy; <a href="http://openseamap.org">OpenSeaMap</a>',
    }
  },
  methods: {
    cleanDate: function(date) {
      if (date === null) {
        return 'Inconnue'
      }
      return date
    },
    iconColor: function(date) {
      if (date === null) {
        return 'blue'
      }
      date = new Date(date)
      const today = new Date()
      if (today <= this.addDays(date, 2)) {
        return 'red'
      }
      if (today <= this.addDays(date, 8)) {
        return 'orange'
      }
      return 'blue'
    },
    addDays: function(date, days) {
      let result = new Date(date)
      result.setDate(result.getDate() + days)
      return result
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
            icon: L.icon({
              iconRetinaUrl: `static/images/markers-${this.iconColor(el['valid_from'])}@2x.png`,
              shadowRetinaUrl: 'static/images/markers-shadow@2x.png',
              iconSize: [36, 46],
              shadowSize: [34, 16],
            }),
            tooltip: `
              <div class="title">
                ${el['title']}
              </div>
              <div class="block">
                ${el['content']}
              </div>
              <div class="block small">
                Dates prévues : ${this.cleanDate(el['valid_from'])} — ${this.cleanDate(el['valid_until'])}
              </div>
              <div class="more small">
                Plus d'informations sur <a href="${el['url']}">le site de la préfecture maritime</a>.
              </div>
              `
          })
        })
    })
    })
  },
}
</script>
<style>
  .title {
    padding-bottom: 0.5em;
    border-bottom: 2px solid #0B3F94;
    font-weight: bold;
    margin-bottom: 1.5em;
  }
  .block {
    border: 1px solid #efefef;
    padding: 1em .5em;
    margin-bottom: 1.5em;
  }
  .small {
    font-size: .9em;
  }
  .more {
    padding: 1em .5em;
    color: #555;
  }
</style>
