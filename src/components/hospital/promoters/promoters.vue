<template>
  <div :class="[childBottomShow? 'hospitalBottom':'']" ref="promotersRef" id=“promotersViews>
    <keep-alive>
      <router-view class="appView" id="promoters" />
    </keep-alive>
    <van-tabbar v-model="active" route :style="{'padding-bottom':$store.state.paddingBottom}" v-if="childBottomShow">
      <!-- <router-link :to="{path : '/promoters/hospital_sourceManagement'}"> -->
      <van-tabbar-item replace :to="{path : '/promoters_index',query:{transition:'def',time: new Date().getTime().toString()}}">
          <span>首页</span>
          <img
          slot="icon"
          slot-scope="props"
          :src="props.active ? index.inactive : index.active "
          />
      </van-tabbar-item>
      <van-tabbar-item replace :to="{path : '/promoters/promoters_cilnic',query:{transition:'def',time: new Date().getTime().toString()}}">
          <img
          slot="icon"
          slot-scope="props"
          :src="props.active ? hospital.inactive : hospital.active"
          >
          <span>门诊</span>
      </van-tabbar-item>
      <van-tabbar-item replace :to="{path : '/promoters/promoters_user',query:{transition:'def',time: new Date().getTime().toString()}}">
          <span>我的</span>
          <img
          slot="icon"
          slot-scope="props"
          :src="props.active ? my.inactive : my.active"
          >
      </van-tabbar-item>
    </van-tabbar>
  </div>
</template>

<script>
import {mapActions,mapGetters} from 'vuex'
export default {
  name: 'promoters',
  data(){
  	return{
  	active: 0,
  	index:{
  	    active: require('../../../assets/image/shouye@2x.png'),
  	    inactive: require('../../../assets/image/shouye-blue@2x.png')
  	},
  	hospital:{
  	    active: require('../../../assets/image/menzhen@2x.png'),
  	    inactive: require('../../../assets/image/menzhen-blue@2x.png')
  	},
  	my:{
  	    active: require('../../../assets/image/wode@2x.png'),
  	    inactive: require('../../../assets/image/wode-blue@2x.png')
  	},
  	//主导航栏图片
  	colorImg:[require('../../../assets/image/shouye-blue@2x.png'),
  			require('../../../assets/image/Hospital@2x.png'),
  			require('../../../assets/image/jiyin-gray@2x.png'),
  			require('../../../assets/image/wode@2x.png')],
  	dataValue : '',
  }
  },
  props:['name'],
  created(){
  },
  computed:{
    ...mapGetters(['childBottomShow'])
  },
  methods:{

  },
}
</script>

<style>
#promotersViews {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  height: 100%;overflow-y: scroll;
	touch-action: pan-y;
	-webkit-overflow-scrolling: touch;
  overflow: scroll;

}
.hospitalBottom{
  /* margin-bottom: .55rem; */
}
.appView {
     /* position: absolute; */
     width: 100%;
     height: 100%;
     /* background: #fff; */
     min-height: 100vh;
     transition: transform 0.24s ease-out;
 }
 #app.quickback .appView{
     transition-duration: 10s;
 }
 .slide-left-enter {
     transform: translate(100%, 0);
 }
 .slide-left-leave-active {
     transform: translate(-50%, 0);
 }
 .slide-right-enter {
     transform: translate(-50%, 0);
 }
 .slide-right-leave-active {
     transform: translate(100%, 0);
 }
</style>
