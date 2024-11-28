<template>
   <PanelItem :index="index" :field="field">
            <template #value>
              <div class="rounded form-input-bordered">
                <GMapMap
                    :center="map.center"
                    :zoom="map.zoom"
                    style="height: 20rem;"
                >
                    <GMapMarker :position="map.center" :draggable="false" />
                </GMapMap>
              </div>
            </template>
   </PanelItem>
</template>

<script>
const map = { center: {}, selectedPlace: false }

export default {
    props: ['index', 'resource', 'resourceName', 'resourceId', 'field'],

    data() {
        return {
            map,
            api_key: null,
            latitude: null,
            longitude: null
        }
    },

    mounted() {
        this.map = JSON.parse(this.field.value) || [];
    }
}
</script>
