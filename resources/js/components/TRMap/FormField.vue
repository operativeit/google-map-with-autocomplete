<template>
    <DefaultField
        :field="field"
        :errors="errors"
        :show-help-text="showHelpText"
    >
        <template #default>
           <div class="float-right">
            <a
              v-tooltip="{
                placement: 'bottom',
                distance: 10,
                skidding: 0,
                content: __('Go to current location'),
              }"
              class="rounded inline-block hover:bg-gray-200 dark:hover:bg-gray-800 focus:outline-none focus:ring"
              tabindex="-1"
              @click.stop.prevent="getCurrentLocation"
             >
              <BasicButton component="span">
                <Icon type="location-marker" /> Current location
              </BasicButton>
             </a>
           </div>
          <form-label
            :label-for="field.attribute"
            :class="{ 'mb-2': showHelpText && field.helpText }"
          >
            {{ fieldLabel }}
            <span v-if="field.required" class="text-red text-sm">{{
              __("*")
            }}</span>
          </form-label>
        </template>
        <template #field>
            <div class="rounded form-input-bordered">
                <GMapMap
                    :center="map.center"
                    :zoom="map.zoom"
                    @zoom_changed="zoomChanged"
                    style="height: 20rem;"
                >
                    <GMapMarker
                        :position="map.center"
                        @dragend="markerDragend"
                        :draggable="true"
                    />
                </GMapMap>
            </div>
        </template>
    </DefaultField>
</template>

<script>
import { FormField, HandlesValidationErrors } from 'laravel-nova'

const map = { center: {}, selectedPlace: false }

export default {
    mixins: [FormField, HandlesValidationErrors],

    props: ['resourceName', 'resourceId', 'field'],

    data() {
        return {
            map,
            selectedPlace: false,  
            api_key: null,
            hideLatitude: false,
            hideLongitude: false,
        }
    },
    mounted() {
    },
    computed: {
        /**
         * Return the label that should be used for the field.
         */
         fieldLabel() {
             // If the field name is purposefully an empty string, then let's show it as such
            if (this.fieldName === "") {
                return "";
            }

            return this.fieldName || this.field.name || this.field.singularLabel;
        },
    },
    methods: {
       getCurrentLocation() {
           if(!!navigator.geolocation) {
               let position = navigator.geolocation.getCurrentPosition((pos) => {
                   this.map.center.lat = pos.coords.latitude;
                   this.map.center.lng = pos.coords.longitude;
                   this.map.zoom = 16;
               });
            }
        },
        setInitialValue() {	    
	    this.map = JSON.parse(this.field.value) || [];
        },

        fill(formData) {
            formData.append(this.field.attribute, JSON.stringify(this.map) || [])
        },

        zoomChanged(level) {
	    this.map.zoom = level;
        },

        markerDragend(marker) {
	    this.map.center.lat = marker.latLng.lat()
            this.map.center.lng = marker.latLng.lng()
       },
    }
}
</script>
