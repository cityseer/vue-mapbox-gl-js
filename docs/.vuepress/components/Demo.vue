<template>
  <div>
    <vue-mapbox-map id='map-container'
                    :access-token='accessToken'
                    :interactive='false'
                    :lng='lng'
                    :lat='lat'
                    :zoom='zoom'
                    :pitch='pitch'
                    :bearing='bearing' />
    <div>
      <p>
        Zoom: {{ zoom.toLocaleString() }}
      </p>
      <p>
        Pitch: {{ pitch.toLocaleString() }}
      </p>
      <p>
        Bearing: {{ bearing.toLocaleString() }}
      </p>
    </div>
  </div>
</template>

<style scoped>
  #map-container {
    position: relative;
    margin: 20px 0 20px 0;
    width: 100%;
    min-height: 400px;
  }
</style>

<script>
// mapbox and related css files loaded in config.js head scripts
import VueMapboxMap from '../../../src/components/VueMapboxMap'

export default {
  name: 'VueMapboxMapDemo',
  components: {
    VueMapboxMap
  },
  data () {
    return {
      computing: false,
      accessToken: 'pk.eyJ1Ijoic2hvbmdvbG9sbyIsImEiOiJjazBkdDQ1bW0wYXEyM2RyejB4eW02a2xwIn0.IzZDyzAlR1sHwDvnEv7ouw',
      lng: -73.982,
      lat: 40.768,
      baseZoom: 13,
      basePitch: 20,
      baseBearing: 0,
      offset: 0
    }
  },
  methods: {
    handleScroll () {
      // https://developer.mozilla.org/en-US/docs/Web/Events/scroll#Example
      if (!this.computing) {
        // use RAF to throttle function
        // use arrow function for "this" context
        // NB - avoid calls that will reflow the page...!
        window.requestAnimationFrame(() => {
          // get offset
          let off = Math.round(window.document.documentElement.scrollTop || document.body.scrollTop)
          // normalise
          off = off / (2000 - window.innerHeight)
          if (off > 1) {
            off = 1
          }
          this.offset = off
          // reset
          this.computing = false
        })
        this.computing = true
      }
    }
  },
  computed: {
    zoom () {
      return this.baseZoom + (this.offset * 5)
    },
    pitch () {
      return this.basePitch + (this.offset * 30)
    },
    bearing () {
      return this.baseBearing + (this.offset * 100)
    }
  },
  mounted () {
    window.addEventListener('scroll', this.handleScroll)
  },
  destroyed () {
    window.removeEventListener('scroll', this.handleScroll)
  }
}
</script>
