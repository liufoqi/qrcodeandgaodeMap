<template>
<div>
  <div class="section">
  <input @touchstart="bindInput" placeholder="查找地点、公交、地铁" :value="keywords" />
  </div>
<div class="map_container">
  <map class="map" id="map" :longitude="longitude" :latitude="latitude" scale="16" show-location="true" :markers="markers" :controls="controls"
    @markertap="makertap" @controltap="clickcontrol" @regionchange="mapchange"  @end="mapchange,makertap"  @click="getSelectLocation">
    </map>
</div>
<div class="map_text" v-if="textData.name">
  <div class="map-1" @tap="getRoute">
    <image src="/static/images/jt.png"></image>
    <div>路线</div>
  </div>
  <p class="h1">{{textData.name}}</p>
  <p>{{textData.desc}}</p>
</div>
</div>
</template>
<script>
// Use Vuex
import store from '../../utils/store'
import Wechat from '../../utils/wechat'
import amap from '../../utils/amap'
export default {
  data(){
    return{
      markers: [],
      latitude: '',
      longitude: '',
      textData: {},
      city: '',
      markerId: 0,
      controls: [
       {
        id: 0,
        position: {
          left: 10,
          top: 200,
          width: 40,
          height: 40
        },
        iconPath: "/static/images/circle1.png",
        clickable: true
      }
    ]
    }
  },
  computed: {
    count () {
      return store.state.count
    }
  },
  methods:{
    getSelectLocation(e){
     console.log(e)
    },
    increment () {
      store.commit('increment')
    },
    decrement () {
      store.commit('decrement')
    },
    bindInput() {
       let latitude= this.latitude;
       let longitude= this.longitude;
       let city= this.city;
       let url = `/pages/inputtip/inputtip?city=${city}&lonlat=${longitude},${latitude}`;
       wx.navigateTo({ url });
  },
  makertap(e) {
    let {markerId} = e;
    let  markers = this.markers;
    let marker = markers[markerId];
    this.showMarkerInfo(marker);
    this.changeMarkerColor(markerId);
  },
  showMarkerInfo(data) {
   let { name, address:desc} = data;
    this.textData.name=name;
    this.textData.desc=desc;
  },
  changeMarkerColor(markerId) {
    let  markers=this.markers;
    markers.forEach((item, index) => {
     item.iconPath = "/static/images/marker.png";
    if (index == markerId)  item.iconPath = "/static/images/marker_checked.png";
    })
    this.markers=markers
    this.markerId=markerId
  },
  getRoute() {
    //起始位置
    if (!this.markers.length) return;
    // 终点
    let latitude2=this.markers[markerId].latitude
    let longitude2=this.markers[markerId].longitude
    let url = `/pages/routes/routes?longitude=${this.longitude}&latitude=${this.latitude}&longitude2=${longitude2}&latitude2=${latitude2}&city=${this.city}&name=${this.textData.name}&desc=${this.textData.desc}`;
    wx.navigateTo({ url });
  },
  clickcontrol(e) {
    console.log("回到用户当前定位点");
   this.controlId= e;
    let mpCtx = wx.createMapContext("map");
    mpCtx.moveToLocation();
  },
  mapchange() {
    console.log("改变视野");
  }
  },
  created(){
     amap.getRegeo().then(d => {
        console.log(d);
        let { name, desc, latitude, longitude } = d[0];
        let { city } = d[0].regeocodeData.addressComponent;
        this.city=city
        this.latitude=latitude
        this.longitude=longitude
        this.textData.name=name
        this.textData.desc= desc
      })
      .catch(e => {
        console.log(e);
      })
  }
}
</script>

<style>
.section{
  height: 30px;
  width: 100%;
}
.section input{
  width:90%;
  margin:5px auto;
  border:1px solid #c3c3c3;
  height:30px;
  border-radius: 3px;
  padding: 0 5px;
}
.map_container{
  position: absolute;
  top: 42px;
  bottom: 80px;
  left: 0;
  right: 0;
}
.map{
  width: 100%;
  height: 100%;
}
.map_text{
  position: absolute;
  left: 0;
  right: 0;
  bottom: 0px;
  height: 80px;
  background: #fff;
  padding: 0 15px;
}
text{
  margin: 5px 0;
  display: block;
  font-size:12px;
  text-overflow: ellipsis;
  white-space: nowrap;
  overflow: hidden;
}
.h1{
  margin: 15px 0;
  font-size:15px;
}

.map-1{
  width: 160rpx;
  height: 160rpx;
  border-radius: 120rpx;
  background-color: #4D8AD7;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  color:#ffffff;
  font-size: 34rpx;
  position: absolute;
  top:0rpx;
  right: 40rpx;
}

.map-1 image{
  width: 60rpx;
  height: 80rpx;
}
</style>
