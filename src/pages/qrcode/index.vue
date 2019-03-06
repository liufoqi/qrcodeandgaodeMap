<template>
  <div class="counter-warp">
    <p>Vuex counter：{{ count}}</p>
    <p>
      <button @click="increment">+</button>
      <button @click="decrement">-</button>
    </p>
    <input type="text" placeholder="请输入要生成的内容" id="elText" v-model="text">
     <button  @click="generate">生成二维码</button>
     <div class="qrcode" id="qrcode">
       <canvas class="myqrcode" canvas-id="myQrcode"></canvas>
        <!-- <img :src="src" alt="" style="width: 200px; height: 200px;"> -->
     </div>
   </div>
</template>

<script>
// Use Vuex
import store from '../../utils/store'
import drawQrcode from '../../utils/weapp.qrcode.esm.js'
export default {
  data(){
   return{
     text:'',
   }
  },
  computed: {
    count () {
      this.qrcode()
      return store.state.count
    }
  },
  methods: {
    increment () {
      store.commit('increment')
    },
    decrement () {
      store.commit('decrement')
    },
    generate(){
      // this.qrcode()
    drawQrcode({
     width: 200,
     height: 200,
     canvasId: 'myQrcode',
     // ctx: wx.createCanvasContext('myQrcode'),
     text: this.text,
     // v1.0.0+版本支持在二维码上绘制图片
     image: {
      // imageResource: '../../images/icon.png',
      dx: 70,
      dy: 70,
      dWidth: 60,
      dHeight: 60
    }    
})
 },
 qrcode(){
   console.log('1111')
   drawQrcode({
     width: 200,
     height: 200,
     canvasId: 'myQrcode',
     // ctx: wx.createCanvasContext('myQrcode'),
     text: 'www.baidu.com',
     // v1.0.0+版本支持在二维码上绘制图片
     image: {
      // imageResource: '../../images/icon.png',
      dx: 70,
      dy: 70,
      dWidth: 60,
      dHeight: 60
    }
  })
 }   
},
  created() {
 
}
}
</script>

<style>
.counter-warp {
  text-align: center;
  margin-top: 100px;
}
.home {
  display: inline-block;
  margin: 100px auto;
  padding: 5px 10px;
  color: blue;
  border: 1px solid blue;
}
.qrcode{
  height: 200px;
  width: 200px;
  margin: 20px auto;
}
.myqrcode{
  width: 200px;
  height: 200px;

}
</style>
