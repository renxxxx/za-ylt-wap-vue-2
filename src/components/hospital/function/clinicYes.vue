<template>
	<div id="yes" class="all" @scroll="handleScroll" ref="yes">
		<!-- <van-pull-refresh v-model="pullingDown" @refresh="afterPullDown" style="ovflow:hidden"> -->
			<ul>
			<van-list  v-model="loading" :finished="finished" :finished-text="test"  @load="getNextPage">
				<li v-for="(item,inx) in  items" :key="inx">
					<router-link :to="{path : '/hospital/hospital_detailsPage',query : {patientId : item.itemId,}}">
						<div class="style">
							<div class="contentTitle">
								<img :src="item.img" alt="">
								<span>{{item.realname}}</span>
							</div>
							<div class="contnet_left">
								<span>推送：{{item.pushTime? moment(item.pushTime).format('YYYY-MM-DD'):''}}</span>
								<span>状态：{{item.span}}</span>
							</div>
						</div>
					</router-link>
						<div class="content_right">
							<button :class="item.buttonColor" @click="submitFn(item,$event)">{{item.button}}</button>
						</div>
				</li>
    	</van-list>
			</ul>
		<!-- </van-pull-refresh> -->
		<div class="returnTop" @click="$refs.yes.scrollTop=0;hospitalReturnTopPage = false;" ref="returnTopRef" v-show="hospitalReturnTopPage">
			<img src="../../../assets/image/returnTop.png" alt />
			<span>顶部</span>
		</div>
	</div>
</template>
<script>
import qs from 'qs';
export default {
	name: 'clinicAll',
	data () {
		return {
			// clinicDetails :[],
			loading: true,
			// 加载状态结束
			finished: false,
			// 请求页数
			page : 0,
			noNum: 0,
			yesNum: 0,
			clinicId:'',
			items:[],
			test:'',
			query:'',
			hospitalReturnTopPage:false,
			scrollTop:0,
			pullingDown: false,
		}
	},
	computed:{
	},
	props:['list'],
	components:{
	},
	created () {
	},
   mounted() {
	},
	watch:{
		$route(to,from){
			// window.removeEventListener("scroll", this.handleScrollYes, true);
		}
	},
	activated() {
		this.show()
	},
	methods:{
		afterPullDown() {
			debugger
			setTimeout(() => {
				Object.assign(this.$data, this.$options.data());
			}, 500);
		},
		show(){
			if(this.query != JSON.stringify(this.$route.query)){
				Object.assign(this.$data, this.$options.data());
				// this.getNextPage()
				this.query = JSON.stringify(this.$route.query);
				this.loading = false;
				if(window.plus){
					//plus.navigator.setStatusBarBackground("#ffffff");
					plus.navigator.setStatusBarStyle("dark")
				}
			}
			this.$nextTick(()=>{
				if(this.scrollTop != 0){
					this.$refs.yes.scrollTop = this.scrollTop
				}
			})
			
		},
		// 滑动一定距离出现返回顶部按钮
		handleScroll() {
			this.scrollTop = this.$refs.yes.scrollTop || this.$refs.yes.pageYOffset
			if (this.scrollTop > 800) {
				this.hospitalReturnTopPage = true;
			} else {
				this.hospitalReturnTopPage = false;
			}
		},
		submitFn(_item,_button){
			this.$axios.post('/c2/patient/confirmjiuzhen',qs.stringify({
				patientId : _item.itemId
			}))
			.then(res =>{
				if(res.data.codeMsg){
					this.$toast({duration: 1000,message: res.data.codeMsg})
				}else{
					this.$toast.success({duration: 1000,message: '操作成功'})
					if(_item.status == 1){
						
						_button.target.style.cssText="color:#333333; background-color:#EEEEEE;"
						_button.target.innerHTML = '已就诊';
					}
				}
			})
			.catch((err)=>{
				
			})
		},
		search(){
			debugger
			let clinicId = '';
			this.$route.query.clinicId? clinicId = this.$route.query.clinicId: clinicId = this.$store.state.hospital.login.clinicId;
			this.$route.name == 'hospital_sourceManagement'?	clinicId='':'',
			this.$route.name == 'outpatient_search'?	clinicId='':'',
			Object.assign(this.$data, this.$options.data());
			this.getNextPage()
		},
		getData(){
			debugger
			let clinicId = '';
			this.$route.query.clinicId? clinicId = this.$route.query.clinicId: clinicId = this.$store.state.hospital.login.clinicId;
			this.$route.name == 'hospital_sourceManagement'?	clinicId='':'',
			this.$route.name == 'outpatient_search'?	clinicId='':''
			this.$axios.post('/c2/patient/items',qs.stringify({
				kw : this.list.keywords,
				hospitalId : this.$store.state.hospital.login.hospital.hospitalId,
				clinicId : clinicId,
				status :4,
				pn : this.page,
				ps : 10,
			}))
			.then(_d => {
				if(_d.data.data.items.length != 0){
					for (let nums in _d.data.data.items) {
						this.items.push({
							clinicName : _d.data.data.items[nums].clinicName,
							itemId : _d.data.data.items[nums].itemId,
							pushTime : _d.data.data.items[nums].pushTime,
							realname : _d.data.data.items[nums].realname,
							status : _d.data.data.items[nums].status,
							img :require( "../../../assets/image/blue@2x.png"),
							button : "已就诊",
							buttonColor : "buttonColor",
							span : "已就诊"
						});
					}
					// 加载状态结束
					this.loading = false;
				}else{
					this.loading = false;
        			this.test='没有更多了'
					this.finished = true;
				}
				if(!this.items.length){
					this.test='无数据'
				}else{
					this.test='没有更多了'
				}
			})
			.catch((err)=>{});

		},
		getNextPage(){
			 this.page++;
			 this.getData()
		},
		initData() {
			debugger
			Object.assign(this.$data, this.$options.data());
			this.getNextPage();
		},
	},
}
</script>

<style scoped>
.all{
	width: 100%;
	/* height: calc(100vh - .85rem); */
	touch-action: pan-y;
    -webkit-overflow-scrolling: touch;
    overflow: scroll;
    overflow-x: hidden;
}
.all li{
	height:.84rem;
	width: 91.5%;
	background-color: #FFFFFF;
	margin: .12rem auto;
	border-radius: .14rem;
	box-shadow: hsla(0, 0%, 0%, 10%) 0rem 0rem 0.1rem 0rem;
	position: relative;
}
.style{
	width: 100%;
	height: 100%;
}
.contentTitle{
	padding: .09rem 0rem .07rem .1rem;
}
.contentTitle img{
	width: .17rem;
	height: .17rem;
}
.contentTitle span{
	margin-left: .05rem;
	font-weight: bold;
}
.contnet_left{
	margin-left: .32rem;
	float: left;
}
.contnet_left span{
	display: block;
}
.contnet_left span:last-child{
	/* margin-top: .04rem; */
}
.content_right{
	float: right;
	margin-right: .15rem;
	margin-top: -.03rem;
}
.content_right button{
	width: .8rem;
	height: .28rem;
	line-height: .28rem;
	color: #FFFFFF;
	background-color: #2B77EF;
	border: none;
	border-radius: .14rem;
	font-size: .14rem;
}
.buttonColor{
    color: #333333!important;
    background-color: #EEEEEE!important;
}
.clinicList{
	margin: 0 .12rem;
}
.clinicList li {
	height:1.01rem;
  width: 100%;
	margin-top:.12rem;
	background-color:#FFFFFF;
	position:relative;
	/*padding:.14rem .15rem;*/
}
.clinicList li p{
	position:absolute;
	bottom:0;
	height:.5rem;
	width:93%;
	line-height:.5rem;
	margin-left:.14rem;
	border-top:1px solid #E5E5E5;
}
.content_left{
	float:left;
	height:.5rem;
	width: 100%;
	margin-left:.15rem;
	margin-top:.14rem;
}
.content_right{
	position: absolute;
	height:.5rem;
	line-height: .5rem;
	right:.14rem;
	bottom:0rem;
}
.content_right img{
	width:.11rem;
	height:.11rem;
	margin-right:.04rem;
}
.yes{
	color: #4DD865;
}
.no{
	color: #2B77EF;
}
</style>
