<template>
	<div class="integralHistory">
    <div class="topNav" :style="{'padding-top':$store.state.paddingTop}">
    	<div class="leftImg" @click="goBackFn"  id="navback">
    		<img src="../../../assets/image/shape@3x.png" alt="">
    	</div>
    	<div class="centerTitle">
    		<h3>兑换记录</h3>
    	</div>
    	<div class="right"></div>
    </div>
	<div class="zhangwei"></div>
	<div class="detailsTitle" :style="{'padding-top':$store.state.paddingTop}">
		<img src="../../../assets/image/lishi.png" alt="">
		<span>兑换历史记录</span>
	</div>
	<div class="integralHistoryList"  @scroll="handleScroll" ref="integralHistoryList">
		<!-- <van-pull-refresh v-model="pullingDown" @refresh="afterPullDown"> -->
			<van-list v-model="loading" :finished="finished" finished-text="没有更多了" @load="onLoad">
				<ul>
					<li v-for="(item,inx) in integralHistory" :key="inx">
						<img :src="item.cover" alt="">
						<div class="ulTitle">
						<h4>{{item.name}}</h4>
						<p>{{item.unitExchangePoint}} 积分/个</p>
						<p>{{moment(item.orderTime).format('YYYY-MM-DD hh:mm')}}</p>
						<span>数量 <strong>{{item.count}}</strong></span>
						</div>
					</li>
				</ul>
			</van-list>
		<!-- </van-pull-refresh> -->
	</div>
    <div class="returnTop" @click="$refs.integralHistoryList.scrollTop=0;hospitalReturnTopPage = false;" ref="returnTopRef" v-show="hospitalReturnTopPage">
		<img src="../../../assets/image/returnTop.png" alt />
		<span>顶部</span>
	</div>
  </div>
</template>

<script>
import qs from 'qs';
export default {
	name: 'integralHistory',
	data () {
		return {
			integralHistory : [],
			loading: false,
			finished: false,
			page: 0,
			hospitalReturnTopPage:false,
			scrollTop:0,
			pullingDown: false,
		}
	},
	computed:{
	},
	created(){
	},
	mounted() {
	},
  	activated(){
		if(this.query != JSON.stringify(this.$route.query)){
			this.initData()
			this.query = JSON.stringify(this.$route.query);
			if(window.plus){
				//plus.navigator.setStatusBarBackground("#ffffff");
				plus.navigator.setStatusBarStyle("dark")
			}
		}
		if(this.scrollTop != 0){
			this.$refs.integralHistoryList.scrollTop = this.scrollTop;
		}
  	},
  	methods: {
	  	afterPullDown() {
		  //下拉刷新
			setTimeout(() => {
				this.pullingDown = false;
				this.initData();
			}, 500);
		},
		initData(){
			Object.assign(this.$data, this.$options.data());
			this.onLoad()
		},
	// 滑动一定距离出现返回顶部按钮
		handleScroll() {
			this.scrollTop = this.$refs.integralHistoryList.scrollTop || this.$refs.integralHistoryList.pageYOffset
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
			this.$axios.post('/clientend2/clinicend/pointexchange/orderdetails',qs.stringify({
				clinicId : this.$store.state.outpatient.login.clinicId,
				pn: this.page,
				ps: 10
			}))
			.then(res => {

				if(!res.data.codeMsg){
			if(res.data.data.items.length != 0){
			for(let i in res.data.data.items){
				this.integralHistory.push(res.data.data.items[i]);
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
			.catch((err)=>{})
		}
  	},
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.integralHistory{
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
.integralHistory ul{
  width: 100%;
  background-color: #FFFFFF;
}
.integralHistory ul li{
  width: 95.733%;
  height: .88rem;
  margin-left: 4.267%;
  border-bottom: 1px solid #EEEEEE;
  position: relative;
}
.integralHistory ul li img{
  width: .66rem;
  height: .66rem;
  object-fit: cover;
  margin-top: .11rem;
  margin-right: .14rem;
  float: left;
}
.ulTitle h4{
  font-weight: bold;
  font-size: .13rem;
  padding-top: .12rem;
}
.ulTitle p{
  color: #999999;
}
.ulTitle p:nth-child(3){
  padding-top: .11rem;
}
.ulTitle span{
  color: #999999;
  font-size: .16rem;
  position: absolute;
  right: .16rem;
  bottom: .2rem;
}
.ulTitle span strong{
  color: #333333;
}
.integralHistoryList{
	width: 100%;
	height: calc(100vh - 1.01rem);
	touch-action: pan-y;
	-webkit-overflow-scrolling: touch;
  	overflow: scroll;
  	overflow-x: hidden;
}
</style>
