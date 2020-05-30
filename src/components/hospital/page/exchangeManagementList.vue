<template>
	<div class="exchangeList">
		<div class="topNav" :style="{'padding-top':$store.state.paddingTop}">
			<div class="leftImg" @click="goBackFn"  id="navback">
				<img src="../../../assets/image/shape@3x.png" alt="">
			</div>
			<div class="centerTitle">
				<h3>门诊兑换清单</h3>
			</div>
			<div class="right"></div>
		</div>
		<div class="zhangwei"></div>
		<div class="exchangeList_content" @scroll="handleScroll" ref="exchangeList_content">
			<ul :style="{'padding-top':$store.state.paddingTop}">
				<van-pull-refresh v-model="pullingDown" @refresh="afterPullDown" >
				<van-list  v-model="loading" :finished="finished" finished-text="没有更多了"  @load="onLoad">
					<li v-for="(item,inx) in exchangeList" :key='inx' class='List' @click="$router.push({path:'/hospital/hospital_exchangeDetails',query:{item : item,time: new Date().getTime()}})">
						<!-- <router-link :to="{path : '/hospital/hospital_exchangeDetails' ,query : {item : item}}"> -->
							<ul>
								<li>
									<span>订单编号：{{item.orderId}}</span>
								</li>
								<li>
									<h4>{{item.clinicName}}</h4>
									<p>兑换时间：<span>{{moment(item.addTime).format('YYYY-MM-DD hh:mm')}}</span></p>
									<p>兑换数量：<span>{{item.totalCount}}</span></p>
								</li>
								<li>
									<p>使用积分：<span>{{item.totalExchangePoint}}</span></p>
								</li>
							</ul>
						<!-- </router-link> -->
					</li>
				</van-list>
				</van-pull-refresh>
			</ul>
		</div>
		<div class="returnTop" @click="$refs.exchangeList_content.scrollTop=0;hospitalReturnTopPage = false;" ref="returnTopRef" v-show="hospitalReturnTopPage">
			<img src="../../../assets/image/returnTop.png" alt />
			<span>顶部</span>
		</div>
	</div>
</template>

<script>
import qs from 'qs';
export default {
	name: 'exchangeList',
	data () {
		return {
			exchangeList:[],
			page : 0,
			loading: false,
			// 加载状态结束
			finished: false,
			//显示下拉加载
			isLoading: false,
			query:{},
			pullingDown:false,
			scrollTop:0,
    		hospitalReturnTopPage:false,
		}
	},
	computed:{
	},
	components:{
	},
	created(){
	},
  	mounted() {
	},
	activated() {
		if(this.query != JSON.stringify(this.$route.query)){
			Object.assign(this.$data, this.$options.data());
			this.query = JSON.stringify(this.$route.query);
			if(window.plus){
				plus.navigator.setStatusBarStyle("dark")
			}
			this.onLoad();
		}
		if(this.scrollTop != 0){
			this.$refs.exchangeList_content.scrollTop = this.scrollTop;
		}
	},
	methods: {
		// 滑动一定距离出现返回顶部按钮
		handleScroll() {
			this.scrollTop = this.$refs.exchangeList_content.scrollTop || this.$refs.exchangeList_content.pageYOffset
			if (this.scrollTop > 800) {
				this.hospitalReturnTopPage = true;
			} else {
				this.hospitalReturnTopPage = false;
			}
		},
		afterPullDown() {
			//下拉刷新
		  setTimeout(() => {
			this.pullingDown = false;
			 this.initData();
		  }, 500);
		},
		initData() {
			 Object.assign(this.$data, this.$options.data());
			let thisVue = this
			if(this.$route.meta.auth && !this.$store.state.hospital.login)
			this.$toast({message:'请登录',onClose:function(){
				thisVue.$router.replace({ path : '/hospital/hospitalLogin',query:{time:1}});
			}})
		  	this.onLoad();
		},
		goBackFn(){
			this.$router.back()
		},
		successFn(_d){
			for (let nums in _d.data.data.items) {
				this.exchangeList.push( _d.data.data.items[nums] );
			}
		},
		getdata(){
			this.$axios.post('/clientend2/hospitalend/exchangemanage/orders',qs.stringify({
				hospitalId : this.$store.state.hospital.login.hospital.hospitalId,
				pn : 1,
				ps : 10,
			}))
			.then(_d => {
				this.finished = false;
				this.isLoading = false;
				this.loading = false;
				this.exchangeList =[];
				this.page = 2;
				
				if( _d.data.data.items.length == 0){
					this.isLoading = false;
					// 加载状态结束
					this.loading = false;
					this.finished = true;
				}else{
					_d.data.codeMsg? this.$toast.success(_d.data.codeMsg) : this.successFn(_d);
					// 
					this.isLoading = false;
					// 加载状态结束
					this.loading = false;
				}
			})
			.catch((err)=>{})
		},
		nextdata(){
			this.$axios.post('/clientend2/hospitalend/exchangemanage/orders',qs.stringify({
				hospitalId : this.$store.state.hospital.login.hospital.hospitalId,
				pn : this.page,
				ps : 10,
			}))
			.then(_d => {
				if(_d.data.data.items.length != 0){
					for (let nums in _d.data.data.items) {
						this.exchangeList.push( _d.data.data.items[nums] );
					}
					this.isLoading = false;
					// 加载状态结束
					this.loading = false;
				}else{
					this.loading = false;
					this.finished = true;
				}
			})
			.catch((err)=>{})
		},
		onLoad(){
			++this.page;
			this.nextdata()
		},
		refresh(){
			this.getdata()
		}
	},
}
</script>

<style scoped>
.exchangeList{
	width: 100%;
	/* height: 100%; */
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
>>>.van-pull-refresh{
	height: 100%;
}
.exchangeList_content{
	height: calc(100% - .47rem);
	touch-action: pan-y;
	-webkit-overflow-scrolling: touch;
	overflow: scroll;
	overflow-x: hidden;
	width: 100%;
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
	float:right;
}
.exchangeList ul{
	width: 100%;
}
.List{
	width: 93.6%;
	height: 1.925rem;
	margin: 0rem auto;
	margin-top: .12rem;
	background-color: #FFFFFF;
}
.List ul{
	width: 91.453%;
	height: 100%;
	margin: 0rem auto;
}
.List ul li:first-child{
	width: 100%;
	height: .425rem;
	line-height: .425rem;
	color: #999999;
	border-bottom: 1px solid #EEEEEE;
  font-size: .135rem;
}
.List ul li:nth-child(2){
	width: 100%;
	height: .75rem;
	border-bottom: 1px solid #EEEEEE;
	padding: .12rem 0rem;
}
.List ul li:nth-child(2)>h4{
	font-weight: bold;
	font-size: .15rem;
	line-height: .34rem;
}
.List ul li:nth-child(2)>p{
	color: #999999;
	line-height: .21rem;
	font-size: .13rem;
}
.List ul li:last-child{
	height: .49rem;
	line-height: .49rem;
	text-align: right;
}
.List ul li:last-child>p{
	color: #999999;
	font-size: .13rem;
}
.List ul li:last-child>p>span{
	color: #FF951B;
}
>>>.van-pull-refresh {
    overflow: hidden;
    -webkit-user-select: none;
    user-select: none;
	/* padding-top: .5rem; */
}
</style>
