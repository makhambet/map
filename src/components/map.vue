<template>
    <div>
        <l-map
            :crs="layer.crs"
            :zoom="zoom"
            :center="center"
            style="height: 100vh; width: 100%"
        >
            <l-tile-layer
                :url="layer.url"
                :subdomains="layer.subdomains"
                :attribution="layer.attribution"
            />
            <l-geo-json
                v-if="show"
                :geojson="geojson"
                :options="options"
                :options-style="styleFunction"
            />
            <l-polygon
                :lat-lngs="polygon.latlngs"
                :color="polygon.color"
            >
                <l-popup content="Polygon" />
            </l-polygon>
            <l-marker :lat-lng="marker" />
        </l-map>
    </div>
</template>

<script>
    import { CRS, latLng } from "leaflet";
    import { LMap, LTileLayer, LMarker, LGeoJson, LPolygon, LPopup } from "vue2-leaflet";

    export default {
        name: "Example",
        components: {
            LMap,
            LTileLayer,
            LGeoJson,
            LMarker,
            LPopup,
            LPolygon
        },
        data() {
            return {
                loading: false,
                show: true,
                enableTooltip: true,
                zoom: 6,
                minZoom: 9,
                center: [47.31322, -1.319482],
                geojson: null,
                fillColor: "rgba(0,0,0,0)",
                // url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
                // attribution:
                //     '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
                layers: [
                    { 
                        url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
                        attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors'
                    },
                    {
                        url: 'https://vec01.maps.yandex.net/tiles?l=map&x={x}&y={y}&z={z}',
                        subdomains: '1,2,3,4',
                        attribution: '&copy; <a href="http://yandex.ru/copyright">Yandex</a>',
                        crs: CRS.EPSG3395
                    },
                    {
                        url: 'https://{s}.tile.opentopomap.org/{z}/{x}/{y}.png',
                        attribution:
                            'Map data: &copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>, <a href="http://viewfinderpanoramas.org">SRTM</a> | Map style: &copy; <a href="https://opentopomap.org">OpenTopoMap</a> (<a href="https://creativecommons.org/licenses/by-sa/3.0/">CC-BY-SA</a>)',
                    },
                    {
                        url: 'https://api.mapbox.com/styles/v1/mapbox/outdoors-v9/tiles/256/{z}/{x}/{y}?access_token=pk.eyJ1IjoiYXJ0YW1vbm92ZGV2IiwiYSI6ImNpcWxjZDVzYzAwMDdpMm5rd2ExcWU3dGIifQ.Jb7HrbPnDjv7SSFxY1bV5Q',
                        attribution: '&copy; Mapbox'
                    },
                    { 
                        url: 'https://api.mapbox.com/styles/v1/mapbox/streets-v9/tiles/256/{z}/{x}/{y}?access_token=pk.eyJ1IjoiYXJ0YW1vbm92ZGV2IiwiYSI6ImNpcWxjZDVzYzAwMDdpMm5rd2ExcWU3dGIifQ.Jb7HrbPnDjv7SSFxY1bV5Q',
                        attribution: '&copy; Mapbox'
                    },
                    { 
                        url: 'https://{s}.tile.thunderforest.com/cycle/{z}/{x}/{y}.png',
                        attribution: '&copy; Thunderforest'
                    },
                    { 
                        url: 'https://{s}.tile.thunderforest.com/landscape/{z}/{x}/{y}.png',
                        attribution: '&copy; Thunderforest'
                    },
                    { 
                        url: 'https://{s}.tile.thunderforest.com/outdoors/{z}/{x}/{y}.png',
                        attribution: '&copy; Thunderforest'
                    },
                    { 
                        url: 'https://mt0.google.com/vt/lyrs=p&hl=en&x={x}&y={y}&z={z}&s=Ga',
                        attribution: '&copy; Google',
                        crs: CRS.EPSG3395
                    },
                    { 
                        url: 'https://stamen-tiles-{s}.a.ssl.fastly.net/toner/{z}/{x}/{y}{r}.png',
                        attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors'
                    },
                    { 
                        url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
                        attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors'
                    },
                ],
                marker: latLng(47.41322, -1.219482),
                polygon: {
                    latlngs: [
                    [48.2263299, -1.6222],
                    [48.21024000000001, -1.6270065],
                    [48.1969447, -1.6136169],
                    [48.18527929999999, -1.6143036],
                    [48.1794457, -1.6098404],
                    [48.1775788, -1.5985107],
                    [48.1676598, -1.5753365],
                    [48.1593731, -1.5521622],
                    [48.1593731, -1.5319061],
                    [48.1722111, -1.5143967],
                    [48.1960115, -1.4841843],
                    [48.2095404, -1.4848709],
                    [48.2291277, -1.4683914],
                    [48.2533687, -1.5116501],
                    [48.2577961, -1.5531921],
                    [48.26828069, -1.5621185],
                    [48.2657179, -1.589241],
                    [48.2589612, -1.6204834],
                    [48.237287, -1.6266632],
                    [48.2263299, -1.6222]
                    ],
                    color: "green"
                },
                layerIndex: 8,
            };
        },
        computed: {
            layer () {
                return this.layers[this.layerIndex]
            },
            options() {
                return {
                    onEachFeature: this.onEachFeatureFunction
                };
            },
            styleFunction() {
                const fillColor = this.fillColor; // important! need touch fillColor in computed for re-calculate when change fillColor
                return () => {
                    return {
                    weight: 2,
                    color: "#ff00ff",
                    opacity: 1,
                    fillColor: fillColor,
                    fillOpacity: 1
                    };
                };
            },
            onEachFeatureFunction() {
                if (!this.enableTooltip) {
                    return () => {};
                }
                return (feature, layer) => {
                    layer.bindTooltip(
                    "<div>code:" +
                        feature.properties.code +
                        "</div><div>nom: " +
                        feature.properties.nom +
                        "</div>",
                    { permanent: false, sticky: true }
                    );
                };
            }
        },
        async created() {
            this.loading = true;
            const response = await fetch("https://rawgit.com/gregoiredavid/france-geojson/master/regions/pays-de-la-loire/communes-pays-de-la-loire.geojson")
            const data = await response.json();
            this.geojson = data;
            this.loading = false;
        }
    };
</script>
