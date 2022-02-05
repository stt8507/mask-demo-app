<template>
    <div class="mask-map" id="mask-map"></div>
</template>

<script>
import L from 'leaflet';

export default {
    name: 'maskMap',
    data() {
        return {
            map: {},
            markers: [],
        };
    },
    computed: {
        currDistrictInfo() {
            return this.$store.getters.currDistrictInfo;
        },
        filteredStores() {
            return this.$store.getters.filteredStores;
        },
    },
    watch: {
        currDistrictInfo(dist) {
            this.map.panTo(new L.LatLng(dist.latitude, dist.longitude));
        },
        filteredStores(stores) {
            this.clearMarkers();
            stores.forEach((element) => this.addMarker(element));
        },
    },
    methods: {
        addMarker(item) {
            
            const ICON = {
                iconUrl: 'https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-violet.png',
                shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/0.7.7/images/marker-shadow.png',
                iconSize: [25, 41],
                iconAnchor: [12, 41],
                popupAnchor: [1, -34],
                shadowSize: [41, 41],
            };

            const marker = L.marker([item.longitude, item.latitude], ICON)
            .addTo(this.map)
            .bindPopup(`<h2 class="popup-name">${item.name}</h2>`);

            marker.markerId = item.id;
            marker.lng = item.longitude;
            marker.lat = item.latitude;

            this.markers.push(marker);
        },
        clearMarkers() {
            this.map.eachLayer((layer) => {
                if (layer instanceof L.Marker) {
                    this.map.removeLayer(layer);
                }
            });
            this.markers.length = 0;
        },
        triggerPopup(markerId) {
            const marker = this.markers.find((d) => d.markerId === markerId);

            this.map.flyTo(new L.LatLng(marker.lng, marker.lat), 15);
            marker.openPopup();
        },
    },
    mounted() {
        this.map = L.map('mask-map', {
            center: [25.03, 121.55],
            zoom: 14, 
        });
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '<a target="_blank" href="https://www.openstreetmap.org/">© OpenStreetMap 貢獻者</a>',
            maxZoom: 18,
        }).addTo(this.map);
    },
};
</script>