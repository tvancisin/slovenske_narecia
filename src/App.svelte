<script>
  import { onMount } from "svelte";
  import * as L from "leaflet";
  import "leaflet/dist/leaflet.css"; // Import Leaflet CSS

  let map; // Leaflet map instance
  let mapContainer; // Reference to the map container

  // Initialize the map
  onMount(async () => {
    // Create the Leaflet map
    map = L.map(mapContainer, {
      center: [48.669, 19.699],
      zoom: 8,
      layers: [
        L.tileLayer(
          // "https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png",
          "https://tile.openstreetmap.org/{z}/{x}/{y}.png",
          {
            attribution:
              '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
          },
        ),
      ],
    });

    // Load and add GeoJSON data
    try {
      const response = await fetch("zahorie_simplified.json"); // Replace with your GeoJSON file path
      const geojsonData = await response.json();

      // Add GeoJSON layer to the map
      L.geoJSON(geojsonData, {
        style: (feature) => {
          return {
            color: "black", // Border color
            weight: 1, // Border thickness
            fillColor: "black", // Fill color
            fillOpacity: 0.3, // Adjust fill opacity
          };
        },
        onEachFeature: (feature, layer) => {
          // Add interactivity

          // if (feature.properties && feature.properties.narecie) {
          //   layer.bindPopup(`Country: ${feature.properties.narecie}`);
          // }

          // Add a click event listener to zoom to the polygon bounds
          layer.on("click", () => {
            const bounds = layer.getBounds(); // Get the bounds of the polygon
            map.fitBounds(bounds); // Zoom to the bounds
          });

          // Add hover events to adjust opacity
          layer.on("mouseover", () => {
            layer.setStyle({ fillOpacity: 0.6 }); // Increase opacity on hover
          });

          layer.on("mouseout", () => {
            layer.setStyle({ fillOpacity: 0.3 }); // Reset opacity when not hovered
          });
        },
      }).addTo(map);
    } catch (err) {
      console.error("Failed to load GeoJSON data:", err);
    }

    // Ensure the map resizes with the window
    window.addEventListener("resize", () => {
      map.invalidateSize(); // Updates the map size
    });
  });
</script>

<!-- Map container -->
<div bind:this={mapContainer} id="map"></div>

<style>
  #map {
    width: 100%; /* Full width of the parent container */
    height: 100vh; /* Full height of the viewport */
    margin: 0;
    padding: 0;
  }
</style>
