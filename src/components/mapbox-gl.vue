<template>
    <div>
        <mapbox
            access-token="pk.eyJ1IjoibnlhNG9tIiwiYSI6ImNrY3hpZDd0ZTAwYnkyeWxhbGd1NzVzdHMifQ.zmemiUxiz8M0pDTg6VnVCQ"
            :map-options="options"
            @map-load="loaded"
        />
    </div>
</template>

<script>
    import Mapbox from 'mapbox-gl-vue'    
    import mapboxgl from 'mapbox-gl'
    import MapboxLanguage from '@mapbox/mapbox-gl-language'
    export default {
        data() {
            return {
                options: {
                    style: 'mapbox://styles/mapbox/streets-v11',
                    center: [-77.03238901390978, 38.913188059745586],
                    zoom: 3,
                },
                hoveredStateId: null,
            }
        },
        methods: {
            loaded(map) {
                const language = new MapboxLanguage({
                    defaultLanguage: 'pt'
                });
                map.addControl(language);
                map.addSource('states', {
                    'type': 'geojson',
                    'data':
                    'https://d2ad6b4ur7yvpq.cloudfront.net/naturalearth-3.3.0/ne_110m_admin_1_states_provinces_shp.geojson'
                });
                    
                // Add a layer showing the state polygons.
                map.addLayer({
                    'id': 'states-layer',
                    'type': 'fill',
                    'source': 'states',
                    'paint': {
                        'fill-color': 'rgba(200, 100, 240, 0.4)',
                        'fill-outline-color': 'rgba(200, 100, 240, 1)'
                    }
                });
                    
                // When a click event occurs on a feature in the states layer, open a popup at the
                // location of the click, with description HTML from its properties.
                map.on('click', 'states-layer', function(e) {
                    new mapboxgl.Popup()
                    .setLngLat(e.lngLat)
                    .setHTML(e.features[0].properties.name)
                    .addTo(map);
                });
                    
                // Change the cursor to a pointer when the mouse is over the states layer.
                map.on('mouseenter', 'states-layer', function() {
                    map.getCanvas().style.cursor = 'pointer';
                });
                
                // Change it back to a pointer when it leaves.
                map.on('mouseleave', 'states-layer', function() {
                    map.getCanvas().style.cursor = '';
                });
            }
        },
        components: { 
            Mapbox
        },
    }
</script>

<style scoped>
    #map {
        width: 100%;
        height: 100vh;
        /* overflow: hidden; */
    }
    .mapboxgl-control-container {
        /* display: none; */
    }
</style>