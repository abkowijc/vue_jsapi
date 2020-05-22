<template>
  <div></div>
</template>

<script>
import { loadModules } from "esri-loader";

export default {
  name: "web-map",
  mounted() {
    // lazy load the required ArcGIS API for JavaScript modules and CSS
    loadModules(
      [
        "esri/Map",
        "esri/views/MapView",
        "esri/widgets/BasemapToggle",
        "esri/widgets/Home",
        "esri/layers/ImageryLayer",
      ],
      { css: true }
    ).then(([ArcGISMap, MapView, BasemapToggle, Home, ImageryLayer]) => {
      const map = new ArcGISMap({
        basemap: "streets",
      });

      this.view = new MapView({
        container: this.$el,
        map: map,
        center: [-77.113596, 38.883037],
        zoom: 10,
      });

      this.basemapToggle = new BasemapToggle({
        view: this.view,
        nextBasemap: "hybrid",
      });
      this.view.ui.add(this.basemapToggle, "bottom-right");
      this.homeWidget = new Home({
        view: this.view,
      });

      // adds the home widget to the top left corner of the MapView
      this.view.ui.add(this.homeWidget, "top-right");

      var radarLayer = new ImageryLayer({
        url:
          "https://idpgis.ncep.noaa.gov/arcgis/rest/services/radar/radar_base_reflectivity_time/ImageServer",
      });
      radarLayer.opacity = 0.7;
      radarLayer.setRefreshInterval(0.1);
      map.add(radarLayer, 0);
    });
  },
  beforeDestroy() {
    if (this.view) {
      // destroy the map view
      this.view.container = null;
    }
  },
};
</script>

<style scoped>
div {
  padding: 0;
  margin: 0;
  width: 100%;
  height: 100%;
}
</style>
