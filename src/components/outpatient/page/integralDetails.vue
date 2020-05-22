<template>
	<div class="integralDetails">
    <div class="topNav" :style="{'padding-top':$store.state.paddingTop}">
    	<div class="leftImg" @click="goBackFn"  id="navback">
    		<img src="../../../assets/image/shape@3x.png" alt="">
    	</div>
    	<div class="centerTitle">
    		<h3>积分明细</h3>
    	</div>
    	<div class="right"></div>
    </div>
		<div class="zhangwei"></div>
    <div class="detailsTitle" :style="{'padding-top':$store.state.paddingTop}">
      <img src="../../../assets/image/lishi.png" alt="">
      <span>积分使用明细</span>
    </div>
    <div class="integralDetailsList" @scroll="handleScroll" ref="integralDetailsList">
      <van-list v-model="loading" :finished="finished" finished-text="没有更多了" @load="onLoad">
        <ul>
          <li v-for="(item,inx) in integralDetails" :key="inx">
            <h4>{{item.note}}</h4>
            <p>{{moment(item.addTime).format('YYYY-MM-DD hh:mm')}}</p>
            <span>{{item.amount}}</span>
          </li>
        </ul>
      </van-list>
    </div>
    <div class="returnTop" @click="$refs.integralDetailsList.scrollTop=0;hospitalReturnTopPage = false;" ref="returnTopRef" v-show="hospitalReturnTopPage">
			<img src="../../../assets/image/returnTop.png" alt />
			<span>顶部</span>
		</div>
  </div>
</template>

<script>
import qs from 'qs';
export default {
  name: 'integralDetails',
  data () {
    return {
      integralDetails : [],
      loading: false,
      finished: false,
      page: 0,
      hospitalReturnTopPage:false,
			scrollTop:0
    }
  },
  computed:{
  },
  created(){
  },
  components:{
	},
  mounted() {
		// if(window.plus){
		// 	//plus.navigator.setStatusBarBackground("#ffffff");
		// 	plus.navigator.setStatusBarStyle("dark")
		// }
  },
  activated(){
		if(this.query != JSON.stringify(this.$route.query)){
			this.query = JSON.stringify(this.$route.query);
			if(window.plus){
				//plus.navigator.setStatusBarBackground("#ffffff");
				plus.navigator.setStatusBarStyle("dark")
			}
    }
    if(this.scrollTop != 0){
			this.$refs.integralDetailsList.scrollTop = this.scrollTop;
		}
  },
  methods: {
    // 滑动一定距离出现返回顶部按钮
		handleScroll() {
			this.scrollTop = this.$refs.integralDetailsList.scrollTop || this.$refs.integralDetailsList.pageYOffset
			if (this.scrollTop > 800) {
				this.hospitalReturnTopPage = true;
			} else {
				this.hospitalReturnTopPage = false;
			}
		},
    goBackFn(){
      this.$router.back(-1);
    },
    onLoad(){
      ++this.page;
      // 
      this.getdata();
    },
    getdata(){
      this.$axios.post('/clientend2/clinicend/pointexchange/exchangepointdetails',qs.stringify({
      	clinicId : this.$store.state.outpatient.login.clinicId,
      	pn: this.page,
      	ps: 10
      }))
      .then(res => {
      	if(!res.data.codeMsg){
          if(res.data.data.items.length != 0){
            for(let i in res.data.data.items){
              this.integralDetails.push(res.data.data.items[i]);
            }
            this.loading = false;
          }else {
            this.loading = false;
            this.finished = true;
          }
      	}else{
      		this.$toast(res.data.codeMsg)
      	}
      })
      .catch((err)=>{
      	//Dialog({ message: err});;
      })
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.integralDetails{
	width: 100%;
	height: 100%;
  overflow: hidden;
	background-color: #F5F5F5;
}
.topNav{
	width: 100%;
	height: .47rem;
	background-color: #FFFFFF;
	position: fixed;
	top:0;
	z-index: 9999;
}
.zhangwei{
	width: 100%;
	height: .47rem;
}
.leftImg{
	width: 10%;
	height: .47rem;
	line-height: .47rem;
	float:left;
}
.leftImg img{
	width: .09rem;
	height: .15rem;
	line-height: .47rem;
	padding-left: .16rem;
}
.centerTitle{
	width: 80%;
	text-align: center;
	height: .47rem;
	line-height: .47rem;
	float:left;
}
.centerTitle h3{
	font-size: .16rem;
	font-weight: bolder;
}
.right{
	width: 10%;
	height: .47rem;
	line-height: .47rem;
	float:left;
}
.detailsTitle{
  width: 100%;
  height: .52rem;
  line-height: .52rem;
  background-color: #FFFFFF;
  border-top: 1px solid #EEEEEE;
  border-bottom: 1px solid #EEEEEE;
}
.detailsTitle img{
  width: .19rem;
  height: .19rem;
  margin-left: .16rem;
  margin-right: .13rem;
}
.detailsTitle span{
  font-size: .14rem;
}
.integralDetails ul{
  width: 100%;
  background-color: #FFFFFF;
}
.integralDetails ul li{
  width: 95.733%;
  height: .64rem;
  margin-left: 4.267%;
  border-bottom: 1px solid #EEEEEE;
  position: relative;
}
.integralDetails ul li h4{
  font-weight: bold;
  font-size: .13rem;
  padding-top: .12rem;
}
.integralDetails ul li p{
  color: #999999;
}
.integralDetails ul li span{
  color: #FFA33A;
  font-size: .16rem;
  position: absolute;
  right: .16rem;
  top: .21rem;
}
.integralDetailsList{
  height: calc(100% - .47rem);
	touch-action: pan-y;
	-webkit-overflow-scrolling: touch;
  overflow: scroll;
	overflow-x: hidden;
}
</style>
