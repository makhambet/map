<template>
    <MglMap
        :accessToken="accessToken"
        :mapStyle.sync="mapStyle"
    >
        <MglNavigationControl position="top-right"/>
        <MglGeolocateControl position="top-right" />
    </Mglmap>



</template>

<script>
    import { MglMap, MglNavigationControl, MglGeolocateControl } from "vue-mapbox";

    export default {
        components: {
            MglMap,
            MglNavigationControl,
            MglGeolocateControl
        },
        data() {
            return {
                accessToken: "pk.eyJ1IjoibnlhNG9tIiwiYSI6ImNrY3hpZDd0ZTAwYnkyeWxhbGd1NzVzdHMifQ.zmemiUxiz8M0pDTg6VnVCQ",
                mapStyle: "mapbox://styles/mapbox/streets-v11"
            };
        }
    };
</script>

<style scoped>

</style>