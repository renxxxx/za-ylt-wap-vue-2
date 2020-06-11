<template>
	<div class="productsExchange">
		<div class="title" v-if="$route.query.show">
			<span>热门兑换</span>
			<!-- <router-link :to="{path : '/outpatient/outpatient_ExchangeList',query:{}}"> -->
			<span @click="$router.push({path:'/outpatient/outpatient_ExchangeList',query:{time: new Date().getTime()}})">更多</span>
			<!-- </router-link> -->
		</div>
		<div class="productsExchangeList" @scroll="handleScroll" ref="productsExchangeList">
			<!-- <van-pull-refresh v-model="pullingDown" @refresh="afterPullDown"> -->
				<van-list  v-model="loading" :finished="finished" finished-text="没有更多了" @load="onLoad">
					<ul>
						<li v-for="(item,inx) in list" :key='inx' @click="$router.push({path:'/outpatient/outpatient_integralShop',query:{commodityId : item.commodityId,time: new Date().getTime()}})">
						<!-- <router-link :to="{path : '/outpatient/outpatient_integralShop',query : {commodityId : item.commodityId,}}"> -->
							<div class="productsImg">
							<img :src="item.cover" alt="">
							</div>
							<h4>{{item.name}}</h4>
							<p><span>{{item.payExchangepoint}}</span> 积分</p>
							<button>立即兑换</button>
						<!-- </router-link> -->
						</li>
					</ul>
				</van-list>
			<!-- </van-pull-refresh> -->
		</div>
		<div class="returnTop" @click="$refs.productsExchangeList.scrollTop=0;hospitalReturnTopPage = false;" ref="returnTopRef" v-show="hospitalReturnTopPage">
			<img src="../../../assets/image/returnTop.png" alt />
			<span>顶部</span>
		</div>
	</div>
</template>

<script>
import axios from 'axios'
import {mapActions,mapGetters} from 'vuex'
import qs from 'qs';
export default {
	name: 'integralExchangeList',
	data () {
		return {
			list : [],
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
	components:{
	},
	created () {

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
			this.$refs.productsExchangeList.scrollTop = this.scrollTop;
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
			// this.onLoad()
		},
		// 滑动一定距离出现返回顶部按钮
		handleScroll() {
			this.scrollTop = this.$refs.productsExchangeList.scrollTop || this.$refs.productsExchangeList.pageYOffset
			if (this.scrollTop > 800) {
				this.hospitalReturnTopPage = true;
			} else {
				this.hospitalReturnTopPage = false;
			}
		},
		onLoad(){
			++this.page;
			this.getdata();
		},
		getdata(){
			this.$axios.post('/clientend2/clinicend/pointexchange/hots',qs.stringify({
				clinicId : this.$store.state.outpatient.login.clinicId,
				pn: this.page,
				ps: 10
			}))
			.then(res => {
				if(!res.data.codeMsg){
				if(res.data.data.items.length != 0){
					for(let i in res.data.data.items){
					this.list.push(res.data.data.items[i]);
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
				//Dialog({ message: err});
			})
		}	
	},
}
</script>

<style scoped>
.productsExchange{
	width: 100%;
	background-color: #FFFFFF;
	/* padding: .12rem 4.8%; */
	/* height: calc(100% - 2.28rem); */
	overflow: hidden;
}
.productsExchangeList{
	height: calc(100% - 0rem);
	touch-action: pan-y;
	-webkit-overflow-scrolling: touch;
  	overflow: scroll;
  	overflow-x: hidden;
}
.title{
	width: 90.4%;
	margin: 0rem auto;
	/* height: .37rem; */
	/* line-height: .37rem */
}
.title span{
	color: #333333;
	font-size: .15rem;
	font-weight: bolder;
	/* margin-bottom: .15rem; */
	display: inline-block;
	height: .37rem;
	line-height: .37rem
}
.title span:last-child{
	float: right;
}
.productsExchangeList ul{
	width: 90.4%;
	margin: .1rem auto;
	/* margin-bottom: .1rem */
}
.productsExchangeList ul li{
	width: 48%;
	height: 2.11rem;
	display: inline-block;
	overflow: hidden;
	text-align: center;
	border: 1px solid #E5E5E5;
	margin-bottom: .09rem;
}
.productsExchangeList ul li:nth-child(2n){
	margin-left: .09rem;
}
.productsImg{
	width: 100%;
	height: 1.14rem;
	margin-bottom: .07rem;
	object-fit: cover;
	overflow: hidden;
}
.productsImg img{
	width: 100%;
	height: 100%;
	object-fit: cover;
}
.productsExchange ul li h4{
	color: #333333;
	font-size: .15rem;
	font-weight: normal;
	padding-bottom: .02rem;
}
.productsExchange ul li p{
	font-size: .13rem;
}
.productsExchange ul li p span{
	color: #FF951B;
}
.productsExchange ul li button{
	width: .8rem;
	height: .21rem;
  line-height: .21rem;
  padding: 0;
	color: #2B77EF;
	border: 1px solid #2B77EF;
	background-color: #FFFFFF;
	border-radius: .1rem;
	margin-top: .12rem;
}
</style>
