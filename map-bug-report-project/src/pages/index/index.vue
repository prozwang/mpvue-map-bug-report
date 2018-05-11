<template>
  <div class="outer">
      <map class="map" :markers="markers" :controls="controls" id="myMap" show-location :longitude="longitude" :latitude="latitude" :scale="scale" @controltap="mapClickAction" @regionchange="mapUpdated" @begin="start" @end="end">
        <cover-view class="control">
          <cover-image class="locate" src="/static/icon_locate.png" @click="init" />
          <cover-image class="call" src="/static/icon_can_call.png" @click="gotoSelectDest" />
          <cover-image class="profile" src="/static/icon_profile.png" @click="goToProfile" />
        </cover-view>
      </map>
  </div>
</template>

<script>
export default {

  data () {
    return {
      latitude: 0,
      longitude: 0,
      scale: 20,
      mapCtx: null,
      markers: []
    }
  },
  computed: {
    controls () {
      console.log('controls called!')
      return [
        {
          id: 1,
          iconPath: '/static/icon_me.png',
          position: {
            width: 24,
            height: 42,
            left: this.screenWidth / 2 - 12,
            top: this.screenHeight / 2 - 21
          }
        },
        {
          id: 2,
          iconPath: '/static/icon_start_bb.png',
          position: {
            width: 115,
            height: 60,
            left: this.screenWidth / 2 - 57,
            top: this.screenHeight / 2 - 80
          },
          clickable: true
        }
      ]
    },
    screenWidth () {
      console.log('screenWidth called')
      return wx.getSystemInfoSync().screenWidth
    },
    screenHeight () {
      console.log('screenHeight called')
      return wx.getSystemInfoSync().windowHeight - 26
    }

  },
  watch: {
    latitude: function () {
      console.log('latitude changed')
    },
    longitude: function () {
      console.log('longitude changed')
    }
  },
  onReady () {
    console.log('onReady')
    this.init()
    this.mapCtx = wx.createMapContext('myMap')
  },
  methods: {

    init () {
      wx.getLocation({
        type: 'gcj02',
        success: res => {
          console.log(res)
          this.latitude = res.latitude
          this.longitude = res.longitude
        }
      })
    },
    getCenter () {
      return new Promise((resolve, reject) => {
        this.mapCtx.getCenterLocation({
          success: res => {
            console.log('success')
            resolve(res)
          },
          fail: res => {
            console.log('fail')
            reject(res)
          }
        })
      })
    },

    async getCenterLocation () {
      let latlng = await this.getCenter()
      console.log('getCenterLocation:', latlng)
      // do not reverse geo when the location in screen center have not changed.
      if (this.latitude === latlng.latitude && this.longitude === latlng.longitude) {
        return
      }
      this.latitude = latlng.latitude
      this.longitude = latlng.longitude
      //若在此处更新marker，虽然模拟器上没问题，但是在真机上依然会出现地图卡顿问题
      // this.setMarker()
    },
    goToProfile () {
      let url = '../profile/main'
      if (this.isLoggedIn) {
        url = '../login/main'
      }
      wx.navigateTo({ url })
    },
    mapUpdated (e) {
      console.log('mapUpdated', e)
    },
    start (e) {
      console.log('start', e)
      this.markers = []
    },
    end (e) {
      console.log('end', e)
      //此时会导致地图无法正确刷新
      this.setMarker()
      this.getCenterLocation()
    },
    setMarker () {
      this.markers = [{
        id: 99,
        iconPath: '/static/icon_me.png',
        width: 24,
        height: 42,
        longitude: this.longitude,
        latitude: this.latitude,
        label: {
          content: '这是一个地图标记',
          color: '#202020',
          fontSize: 16
        }
      }]
    }
  }
}
</script>

<style scoped>
.map {
  height: 100vh;
  width: 100%
  }
.control{
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  display: flex;
  justify-content: space-between;
  align-items: center
}
.locate{
  width: 42px;
  height: 42px;
  padding-right: 30px 
}

.call{
  width: 132px;
  height: 132px
}
.profile{
  width: 72px;
  height: 72px
}

.outer {
  height: 100%;
  width: 100%;
  display: flex;
  flex: 1;
  flex-direction: column;
}
</style>
