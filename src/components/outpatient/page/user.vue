<template>
	<div class="user">
		<div class="user_top">
			<div class="user_set" v-show="false">
				<img src="../../../assets/image/set up@2x.png" alt="">
			</div>
			<div class="user_message">
				<div class="top_left">
					<img :src="coverImg? coverImg:require('../../../assets/image/logo@2x.png')" alt="">
					<span :class="[dataColor? 'yesData':'noData']">{{dataColor? "已认证":"未认证"}}</span>
				</div>
				<div class="top_center">
					<h3>{{this.$store.state.outpatient.login.clinic.name}}</h3>
					<p>账号：{{this.$store.state.outpatient.login.phone}}</p>
					<p>医院：{{this.$store.state.outpatient.login.hospital.name}}</p>
				</div>
				<div class="top_right" @click="showImgFn">
					<span>营业执照</span>
					<img src="../../../assets/image/Chevron Copy 2@2x.png" alt="">
				</div>
			</div>
		</div>
		<van-image-preview v-model="show" :images="images" @change="onChange" >
		  <!-- <template v-slot:index>第{{ index }}页</template> -->
		</van-image-preview>
		<div class="user_center">
			<ul>
				<!-- <router-link :to="{name: 'outpatient_taskCenter',query:{}}"> -->
				<li @click="$router.push({path:'/outpatient/outpatient_taskCenter',query:{time: new Date().getTime().toString()}})">
					<span>任务中心</span>
					<img src="../../../assets/image/Chevron Copy 2@2x.png" alt="">
				</li>
				<!-- </router-link> -->
				<!-- <router-link :to="{name: 'outpatient_integralExchange',query:{}}"> -->
				<li @click="$router.push({path:'/outpatient/outpatient_integralExchange',query:{show:true,time: new Date().getTime().toString()}})">
					<span>积分兑换</span>
					<img src="../../../assets/image/Chevron Copy 2@2x.png" alt="">
				</li>
				<!-- </router-link> -->
					<li @click="exitFn">
						<span>退出登录</span>
						<img src="../../../assets/image/Chevron Copy 2@2x.png" alt="">
					</li>
			</ul>
		</div>
		<span>版本：{{this.$version.split('-')[0]}}</span>
	</div>
</template>

<script>
import qs from 'qs';
export default {
  name: 'user',
  data () {
    return {
		name: 'user',
		coverImg: '',
		show: false,
		index: 0,
		images: [],
		dataColor:0
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
  	},
  methods: {
	  initData(){
			let thisVue = this
			if(this.$route.meta.auth && !this.$store.state.outpatient.login)
			this.$toast({message:'请登录',onClose:function(){
				thisVue.$router.replace({ path : '/outpatientLogin',query:{time:1}});
			}})
			Object.assign(this.$data, this.$options.data());
			this.userFn();
			this.coverImg = this.$store.state.outpatient.login.clinic.cover;
			this.dataColor = this.$store.state.outpatient.login.clinic.authStatus
			if(this.$store.state.outpatient.login.clinic.license)
				this.images.push(this.$store.state.outpatient.login.clinic.license)
	  },
	onChange(index) {
	    this.index = index;
	},
	showImgFn(){

		if(this.images.length>=1){
			this.show = true;
		}else{
			this.$toast("请上传营业执照")
		}
		
	},
	userFn(){
		//用户头像值
		let cover = this.$store.state.outpatient.login.cover;
		// 是否认证值
		let license = this.$store.state.outpatient.login.license
		if(cover == '' || cover == undefined || cover == null){
		}else{
			this.coverImg = cover
		}
	 },
	//退出方法
	exitFn(){
		let thisVue=this
		this.$dialog.confirm({
			message: '是否确定退出门诊端',
		})
		.then( ()=>{
			this.$axios.post('/clinic/logout').then(function(){
				localStorage.removeItem('entrance')
				// localStorage.clear()
				thisVue.$toast("操作成功")
				setTimeout(()=>{
					// thisVue.$router.push({path:"/outpatientLogin",query:{}})
					location.href=location.pathname
				},1500)
			})
		})
		
	}
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.user{
	width: 100%;
	height: 100%;
	background-color: #F5F5F5;
}
.user_top{
	width: 100%;
	height: 1.3rem;
	background-color: #FFFFFF;
	position: relative;
	padding-top: .36rem;
}
.user_set{
	top: .18rem;
	right: .16rem;
	position: absolute;
}
.user_set img{
	width: .21rem;
	height: .2rem;
}
.user_message{
	/* height: .8rem; */
	/* margin: auto 0rem; */
	/* padding: .23rem .18rem .15rem .28rem;; */
}

.top_left{
	width: 24%;
	float:left;
	height: .8rem;
	margin:.23rem 7.7% .15rem 7.4%;
}
.top_left img{
	width: .75rem;
	height: .75rem;
	position: relative;
	border-radius: 50%;
}
.top_left span{
	position: absolute;
	left: .61rem;
	top: 1.15rem;
	font-size: .12rem;
	background-size:100% 100%;
	padding: .02rem .1rem;
	width: .56rem;
    height: 22px;
    box-sizing: border-box;
}
.top_center{
	position: relative;
	width: 36%;
	float: left;
	height: .49rem;
	margin: .37rem .15rem .28rem 0rem;
}
.top_center h3{
	height: .29rem;
	line-height: .29rem;
	font-size: 16px;
	font-weight: bolder;
	display: -webkit-box;
	overflow: hidden;
	text-overflow: ellipsis;
	word-wrap: break-word;
	-webkit-line-clamp: 1;
	-webkit-box-orient: vertical;
}
.top_center p{
	color: #666666;
	font-size: .13rem;
	display: -webkit-box;
	overflow: hidden;
	text-overflow: ellipsis;
	word-wrap: break-word;
	-webkit-line-clamp: 1;
	-webkit-box-orient: vertical;
}
.top_right{
	/* width: 13%; */
	float:left;
	margin: .53rem 0rem .44rem 0rem;
	color: #999999;
	position: relative;
}
.top_right span{
	font-size: .13rem;
}
.top_right img{
	width: .08rem;
	position: absolute;
	left: .56rem;
	height: .13rem;
  bottom: 0rem;
	top: 0rem;
  margin: auto 0rem;
}

.user_center{
	width: 100%;
	background-color: #FFFFFF;
	margin-top: .12rem;
}
.user_center ul{
	height: 100%;
	width: 100%;
	font-size: .14rem;
}

.user_center ul li{
	height: .52rem;
	line-height: .52rem;
	padding: 0rem .18rem;
	position: relative;
	border-bottom: 1px solid #EEEEEE;
}
.user_center ul li img{
	position: absolute;
	height: .13rem;
	width: .08rem;
	right: .18rem;
	top: .19rem;
}
.user>span{
	width: 50%;
	text-align: center;
	color: #cfd2d3;
	position: absolute;
	bottom: .8rem;
	left: 0rem;
	right: 0rem;
	margin: 0rem auto;
}
.yesData{
	color: #FFFFFF;
	background:url('../../../assets/image/Gradualchange@2x.png');
}
.noData{
	color: rgb(255,255,255);
	background: rgb(220,220,220);
	border-radius: 25px;
}
</style>