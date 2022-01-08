<template>
  <div>
    <div class="header-margins">
      Highlight Google Maps polygons on hover (nightprogrammer.com)
    </div>
    <div id="polygon-label-map" class="map-margins"></div>
  </div>
</template>

<script>
import loadGoogleMapsApi from "load-google-maps-api";
import { gMapsApiKey } from "./../constants";
import { polygonCoordsSetData } from "./polygonsets";

export default {
  name: "AnimatedMarkerMap",
  data() {
    return {
      map: null,
    };
  },
  mounted() {
    loadGoogleMapsApi({
      key: gMapsApiKey,
      libraries: ["places", "drawing", "geometry"],
    }).then(() => {
      const mapZoom = 12;
      const { google } = window;
      const mapOptions = {
        zoom: mapZoom,
        mapTypeId: google.maps.MapTypeId.HYBRID,
        center: new google.maps.LatLng({ lat: 28.680272, lng: 76.5314777 }),
        mapTypeControl: true,
        streetViewControl: false,
        mapTypeControlOptions: {
          position: google.maps.ControlPosition.BOTTOM_LEFT,
        },
      };
      this.map = new google.maps.Map(
        document.getElementById("polygon-label-map"),
        mapOptions
      );

      const polygonCoordsSet = polygonCoordsSetData;

      const tempBounds = new google.maps.LatLngBounds();

      for (let i = 0; i < polygonCoordsSet.length; i++) {
        for (let j = 0; j < polygonCoordsSet[i].length; j++) {
          const x = {
            lat: polygonCoordsSet[i].lat,
            lng: polygonCoordsSet[i].lng,
          };
          const BoundLatLng = new google.maps.LatLng({
            lat: parseFloat(x.lat),
            lng: parseFloat(x.lng),
          });
          tempBounds.extend(BoundLatLng);
        }
      }

      const PolygonShapes = [];
      const fillColors = ["#ff0000", "#00ff00", "#00ff00"];

      for (let i = 0; i < polygonCoordsSet.length; i++) {
        const polygonShapeContructor = new google.maps.Polygon({
          paths: polygonCoordsSet[i],
          strokeColor: "#ffffff",
          map: this.map,
          strokeWeight: 3,
          fillColor: fillColors[i],
          fillOpacity: 0.2,
          zIndex: 99999,
        });
        google.maps.event.addListener(
          polygonShapeContructor,
          "mouseover",
          () => {
            polygonShapeContructor.setOptions({
              fillOpacity: 0.8,
            });
          }
        );

        google.maps.event.addListener(
          polygonShapeContructor,
          "mouseout",
          () => {
            polygonShapeContructor.setOptions({
              fillOpacity: 0.2,
            });
          }
        );
        PolygonShapes.push(polygonShapeContructor);
      }

      PolygonShapes.forEach((polygon) => {
        polygon.setMap(this.map);
      });

      this.map.setZoom(7);
    });
  },
};
</script>

<style scoped>
.header-margins {
  margin-left: 40px;
  margin-top: 20px;
}

.map-margins {
  height: 400px;
  width: 600px;
  margin: 30px 40px;
}
</style>