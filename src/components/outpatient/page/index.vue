<template>
  	<div class="index">
		<!-- <van-pull-refresh v-model="pullingDown" @refresh="afterPullDown" > -->
			<div class="navWarp" :style="{'padding-top':$store.state.paddingTop}">
				<!-- 搜索及其筛选 -->
				<div class="topNav" ref="topNav">
					<!-- <router-link :to="{name:'outpatient_pathogenicSearch',query:{time:new Date().getTime().toString()}}"> -->
					<div class="indexSearch" @click="$router.push({path:'/outpatient/outpatient_pathogenicSearch',query:{time: new Date().getTime().toString()}})">
						<input type="text" placeholder="搜索病员" readonly="readonly">
						<img src="../../../assets/image/sousuo@2x.png" alt="">
					</div>
						<!-- </router-link> -->
					<!-- <router-link :to="{name:'outpatient_pathogenicSearch',query:{}}"> -->
					<div class="clinic_buttton" @click="$router.push({path:'/outpatient/outpatient_pathogenicSearch',query:{time: new Date().getTime().toString()}})">
						<button>搜索</button>
					</div>
					<!-- </router-link> -->
					<!-- <router-link :to="{name:'outpatient_pathogenicSearch',query:{show:1}}"> -->
					<div class="indexScreening" @click="$router.push({path:'/outpatient/outpatient_pathogenicSearch',query:{show:1,time: new Date().getTime().toString()}})">
						<span>筛选 </span>
						<img src="../../../assets/image/screen@2x.png" alt="加载中" >
					</div>
					<!-- </router-link> -->
				</div>
				<div id="navType">
					<div class="navType_one" @click="xiaclickFn(0)">
						<h5>新增病员</h5>
					</div>
					<div class="navType_two" @click="xiaclickFn(1)">
						<h5>未就诊{{noItemsNum}}</h5>
					</div>
					<div class="navType_three" @click="xiaclickFn(2)">
						<h5>已就诊{{yesItemsNum}}</h5>
					</div>
					<div ref="xiahuaxian" class="navType_line"></div>
				</div>
			</div>
			<div style="height: .8rem;" :style="{'padding-top':$store.state.paddingTop}"></div>
			<div style="height:calc(100vh - 1.3rem);">
				<div class="lists" v-show="all">
					<div class="newAdd">
						<div class="newAddTitle">
							<img src="../../../assets/image/bitian@2x.png" alt="">
							<h3>必填项</h3>
							<ul class="Fill">
								<li>
									<span>病患姓名</span>
									<input type="text" v-model="account.realname" @focus='focusFn' @blur='blurFn' placeholder="请填写" >
								</li>
								<li>
									<span>联系电话</span>
									<input type="text" v-model="account.tel" maxlength="11" @focus='focusFn' @blur='blurFn' oninput="value=value.replace(/[^\d]/g,'')" placeholder="请填写">
								</li>
								<li>
									<span>身份证号</span>
									<input type="text" v-model="account.idcardNo" maxlength="18" @focus='focusFn' @blur='blurFn' oninput="value=value.replace(/[^\d|xX]/g,'')" placeholder="请填写">
								</li>
							</ul>
						</div>
						<div class="newAddTitle bottom">
							<img src="../../../assets/image/bitian@2x.png" alt="">
							<h3>选填项</h3>
							<ul class="Fill" style="height:auto">
								<li>
									<span>备注</span>
									<input type="text" v-model="account.remark"  placeholder="请填写" >
								</li>
							</ul>
						</div>
						<button class="submitClass" @click="hospitalSubmit">提交</button>
					</div>
				</div>
				<!-- :style="{'padding-top':(parseInt($store.state.paddingTop.replace('px',''))+12)+'px'}" -->
				<div class="list" v-show="yes" @scroll="handleScroll" ref="listYes">
					<!-- <van-pull-refresh v-model="pullingDown" @refresh="afterPullDown" > -->
					<van-list v-model="loading" :finished="finishedYes" finished-text="没有更多了"  @load="yesNextPageFn">
						<ul>
							<li v-for="(item,inx) in yesItems" :key="inx">
								<router-link :to="{path : '/outpatient/outpatient_detailsPage' ,query : {patientId : item.itemId,}}">
									<div class="content_left">
										<span>{{item.realname}}</span>
									</div>
									<div class="content_right">
										<img :src='item.img'>
										<span :class="item.span=='未就诊'? 'no':'yes'">{{item.span}}</span>
									</div>
									<p>{{moment(item.pushTime).format('YYYY-MM-DD HH:mm:ss')}}</p>
								</router-link>
							</li>
						</ul>
					</van-list>
					<!-- </van-pull-refresh> -->
				</div>
				<div class="list" v-show="no" @scroll="handleScroll" ref="listNo">
					<!-- <van-pull-refresh v-model="pullingDown" @refresh="afterPullDown" > -->
					<van-list v-model="loading" :finished="finishedNo" finished-text="没有更多了"  @load="noNextPageFn">
						<ul>
							<li v-for="(item,inx) in noItems" :key="inx">
								<router-link :to="{path : '/outpatient/outpatient_detailsPage' ,query : {patientId : item.itemId,}}">
									<div class="content_left">
										<span>{{item.realname}}</span>
									</div>
									<div class="content_right">
										<img :src='item.img'>
										<span :class="item.span=='未就诊'? 'no':'yes'">{{item.span}}</span>
									</div>
									<p>{{moment(item.pushTime).format('YYYY-MM-DD HH:mm:ss')}}</p>
								</router-link>
							</li>
						</ul>
					</van-list>
					<!-- </van-pull-refresh> -->
				</div>
		</div>
		<!-- </van-pull-refresh> -->
		<div class="returnTop" @click="returnTopFn" ref="returnTopRef" v-show="hospitalReturnTopPage">
			<img src="../../../assets/image/returnTop.png" alt />
			<span>顶部</span>
		</div>
  	</div>
</template>
<script>
import qs from 'qs';
export default {
	name: 'index',
	data () {
		return {
			name: 'index',
			//导航栏切换标题
			// pullingDown: false,
			yesItems:[],
			noItems:[],
			itemsNum:'',
			yesItemsNum:'',
			noItemsNum:'',
			loading: true,
			// 加载状态结束
			finishedYes: false,
			finishedNo:false,
			yesPage:0,
			noPage:0,
			all:true,
			yes:false,
			no:false,
			noData:true,
			yesData:true,
			scrollTopYes:0,
			scrollTopNo:0,
			hospitalReturnTopPage:false,
			clickData:0,
			account:{
				realname:'',
				tel:'',
				idcardNo:'',
				remark:''
			},
			docmHeight: window.innerHeight,  //默认屏幕高度
			showHeight: window.innerHeight,   //实时屏幕高度
			hideshow:true,  //显示或者隐藏footer

		}
	},
	created(){
		
	},

	mounted(){
		
	},

	watch:{
	showHeight: function(newValue) {
		if(this.docmHeight > newValue){
		this.$store.state.bottomShow = false
		}else{
		this.$store.state.bottomShow = true
		}
	}
	},
  	activated(){
		if(this.query != JSON.stringify(this.$route.query)){
			this.initData()
			this.query = JSON.stringify(this.$route.query);
			let width = document.getElementById('navType').getBoundingClientRect().width/6
			this.$refs.xiahuaxian.style.webkitTransform = "translateX("+(width)+"px) translateX(-50%)"
			if(window.plus){
				//plus.navigator.setStatusBarBackground("#ffffff");
				plus.navigator.setStatusBarStyle("dark")
			}
		}
		if(this.scrollTopYes != 0){
			this.$refs.listYes.scrollTop = this.scrollTopYes;
		}
		if(this.scrollTopNo != 0){
			this.$refs.listNo.scrollTop = this.scrollTopNo;
		}
		window.onresize = ()=>{
			return(()=>{
			this.showHeight = window.innerHeight;
			})()
		}

    },
	computed:{
	},
	//注册组件
	components:{
	},
	methods:{
		focusFn(){	
			// console.log('focusFn')
			// this.$store.state.bottomShow = false;
		},
		blurFn(){
			// console.log('blurFn')
			// this.$store.state.bottomShow = true;
		},
		hospitalSubmit(){
			this.$axios.post('/c2/patient/itemadd',qs.stringify({
				clinicId : this.$store.state.outpatient.login.clinic.clinicId,
				hospitalId : this.$store.state.outpatient.login.hospital.hospitalId,
				password : this.account.password,
				realname : this.account.realname,
				tel	:   this.account.tel,
				remark :  this.account.remark,
				idcardNo :  this.account.idcardNo
			}))
			.then( res =>{
				if(res.data.codeMsg){
					this.$toast({ message: res.data.codeMsg });
				}
				if(res.data.code == 0){
					//成功
					this.$toast({ message: '提交成功' });
					this.account = {
						realname:'',
						tel:'',
						idcardNo:'',
						remark:''
					}
				}else{
					//失败
				}
			})
			.catch((err)=>{
				this.$toast('加载失败!')
			})
		},
		// 滑动一定距离出现返回顶部按钮
		handleScroll() {
			switch(this.clickData){
				case 1:
				this.scrollTopNo = this.$refs.listNo.scrollTop || this.$refs.listNo.pageYOffset
				if (this.scrollTopNo > 800) {
					this.hospitalReturnTopPage = true;
				} else {
					this.hospitalReturnTopPage = false;
				}
				break;
				case 2:
				this.scrollTopYes = this.$refs.listYes.scrollTop || this.$refs.listYes.pageYOffset
				if (this.scrollTopYes > 800) {
					this.hospitalReturnTopPage = true;
				} else {
					this.hospitalReturnTopPage = false;
				}
				break;
			}
		},
		returnTopFn(){
			switch(this.clickData){
				case 1:
				this.$refs.listYes.scrollTop = 0;
				break;
				case 2:
				this.$refs.listNo.scrollTop = 0;
				break;
			}
			// this.$refs.indexList.scrollTop=0;
			this.hospitalReturnTopPage = false;
		},
		xiaclickFn(_data){
			let width = document.getElementById('navType').getBoundingClientRect().width/6;
			this.clickData = _data;
			switch(_data){
				case 0:
				this.all = true;
				this.yes = false;
				this.no = false;
				this.$refs.xiahuaxian.style.webkitTransform = "translateX("+(width)+"px) translateX(-50%)"
				break;
				case 1:
				this.all = false;
				this.yes = false;
				this.no = true;
				this.$refs.xiahuaxian.style.webkitTransform = "translateX("+(width*3)+"px) translateX(-50%)"
				if (this.scrollTopNo > 800) {
					this.hospitalReturnTopPage = true;
				} else {
					this.hospitalReturnTopPage = false;
				}
				this.$nextTick(()=>{
					this.$refs.listNo.scrollTop = this.scrollTopNo
				})
				break;
				case 2:
				this.all = false;
				this.yes = true;
				this.no = false;
				this.$refs.xiahuaxian.style.webkitTransform = "translateX("+(width*5)+"px) translateX(-50%)"
				if (this.scrollTopYes > 800) {
					this.hospitalReturnTopPage = true;
				} else {
					this.hospitalReturnTopPage = false;
				}
				this.$nextTick(()=>{
					this.$refs.listYes.scrollTop = this.scrollTopYes
				})
				break;
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
				if(this.$route.meta.auth && !this.$store.state.outpatient.login)
					this.$toast({message:'请登录',onClose:function(){
						thisVue.$router.replace({ path : '/outpatientLogin',query:{time:1}});
					}})
			let width = document.getElementById('navType').getBoundingClientRect().width/6
			this.$refs.xiahuaxian.style.webkitTransform = "translateX("+(width)+"px) translateX(-50%)"
			this.getAllNum();
			this.yesNextPageFn();
			this.noNextPageFn();
		},
		getAllNum() {
			this.getNum(4).then((v)=>{
				this.yesItemsNum = v
			})
			this.getNum(1).then((v)=>{
				this.noItemsNum = v
			})
			
			
		},
		// 获取下一页的方法
		getDataNo(){
			this.$axios.post('/c2/patient/items',qs.stringify({
				hospitalId : this.$store.state.outpatient.login.hospital.hospitalId,
				clinicId : this.$store.state.outpatient.login.clinic.clinicId,
				status: 1,
				pn : this.noPage,
				ps : 10,
			}))
			.then(res => {
				if(res.data.data.items.length != 0){
					for (let nums in res.data.data.items) {
						this.noItems.push({
							clinicName : res.data.data.items[nums].clinicName,
							itemId : res.data.data.items[nums].itemId,
							pushTime : res.data.data.items[nums].pushTime,
							realname : res.data.data.items[nums].realname,
							status : res.data.data.items[nums].status,
							img : require("../../../assets/image/weijiuzhen@2x.png"),
							button : "确认就诊",
							span : "未就诊"
						});
					}
					// 加载状态结束
					this.loading = false;
				}else{
					this.loading = false;
					this.finishedNo = true;
				}
			})
			.catch((err)=>{
						
			});
		},
		// 获取下一页的方法
		getDataYse(){
			this.$axios.post('/c2/patient/items',qs.stringify({
				hospitalId : this.$store.state.outpatient.login.hospital.hospitalId,
				clinicId : this.$store.state.outpatient.login.clinic.clinicId,
				status: 4,
				pn : this.yesPage,
				ps : 10,
			}))
			.then(res => {
				if(res.data.data.items.length != 0){
					for (let nums in res.data.data.items) {
						this.yesItems.push({
							clinicName : res.data.data.items[nums].clinicName,
							itemId : res.data.data.items[nums].itemId,
							pushTime : res.data.data.items[nums].pushTime,
							realname : res.data.data.items[nums].realname,
							status : res.data.data.items[nums].status,
							img :require( "../../../assets/image/yijiuzhen@2x.png"),
							button : "已就诊",
							buttonColor : "buttonColor",
							span : "已就诊"
						});
					}
					// 加载状态结束
					this.loading = false;
				}else{
					this.loading = false;
					this.finishedYes = true;
				}
			})
			.catch((err)=>{
				this.$toast(err)
			});
		},
		
		// 获取下一页的方法
		async getNum(data){
			debugger;
			var num ='';
			await this.$axios.post('/c2/patient/items',qs.stringify({
					hospitalId : this.$store.state.outpatient.login.hospital.hospitalId,
					clinicId : this.$store.state.outpatient.login.clinic.clinicId,
					status: data,
					pn : 1,
					ps : 10,
				}))
				.then(res => {
					num = res.data.data.sum.totalCount;
				})
				.catch((err)=>{
					this.$toast(err)
				});
			
			return num;
		},
		// 已就诊病原列表的下一页
		yesNextPageFn(){
			this.yesPage++;
			this.getDataYse();
		},
		// 未就诊病原列表的下一页
		noNextPageFn(){
			this.noPage++;
			this.getDataNo();
		},
		submitFn(_item,_button){
			this.$axios.post('/c2/patient/confirmjiuzhen',qs.stringify({
				patientId : _item.itemId
			}))
			.then(res =>{
				if(res.data.codeMsg){
					this.$toast({duration: 1000,message: res.data.codeMsg})
				}
				if(res.data.code == 0 ){
					this.$toast.success({duration: 1000,message: '操作成功'})
					if(_item.status == 1){
						
						_button.target.style.cssText="color:#333333; background-color:#EEEEEE;"
						_button.target.innerHTML = '已就诊';
					}
				}
			})
			.catch((err)=>{
				
			})
		}
		
	},
}
</script>
<style scoped>
.index{
	width: 100%;
	height: 100%;
	background-color: #F5F5F5;
	overflow: hidden;
}
.navWarp{
	height: .8rem;
    line-height: .335rem;
    width: 100%;
    /* padding-top: 0.07rem; */
    background: url('/assets/img/BJ-blue.5dbb0cc.png');
    /* background-size: 100% 100%; */
    background-color: #2B77EF;
    position: fixed;
    top: 0rem;
    z-index: 1;
}
.topNav{
	height: .46rem;
	box-sizing: border-box;
}
.indexReturn{
	width: 10%;
	text-align: center;
	/* display: inline-block; */
	z-index: 999;
	padding-top: 6px;
	float: left;
}
.indexReturn img{
	width: .09rem;
	height: .15rem;
	margin: auto;
}
.indexSearch{
	/* display: inline-block; */
	/* width: .43rem; */
	width: 70%;
	float: left;
	/* text-align: center; */
	z-index: 999;
	height: .4rem;
	line-height: .36rem;
	position: relative;
	padding-top: 6px;
	margin-left: .1rem;
}
.indexSearch img{
	position: absolute;z-index: 9;left: .11rem;top: .15rem;
	height: .15rem;width:.14rem;
}
.indexSearch input{
	border-radius: .18rem;border: none;
	height: .335rem;width: 2.4rem;
	line-height: .3rem;
	/* padding: 0; */
	width: 84%;
	padding-left: 12%;
	/* margin:0 5%; */
	background: #F5F5F5;
	font-size:.15rem;
}
.clinic_buttton{
	float: left;
	margin-top: .024rem;
	margin-left: -.05rem;
	padding-top: 6px;
}
.clinic_buttton button{
	color: #FFFFFF;
	background-color: rgba(0,0,0,0);
	border-radius: .15rem;
	border: 1px solid #FFFFFF;
	height: .28rem;
	width: .45rem;
	font-size: .12rem;
	text-align: center;
	line-height: .25rem;
}
.indexScreening{
	display:inline-block;
	/* width: .43rem; */
	width: 12%;
	float: left;
	text-align: center;
	z-index: 999;
	height: .4rem;
	line-height: .38rem;
	margin-left: .052rem;
	padding-top: 6px;
}
.indexScreening span{
	width: .15rem;height: .21rem;
	/* margin: .65rem .02rem .06rem 0rem; */
	color: #FFFFFF;font-size: .12rem;
	z-index: 3;
	position:relative;
}
.indexScreening img{
	height: .13rem;width: .12rem;
	/* margin-right: .16rem; */
}
.indexFrom label{
	background: red;
}
#navType{
	height: .34rem;
	line-height: .34rem;
	/* width: 73.33%; */
	/* margin:0rem auto; */
	color: #FFFFFF;
	box-sizing: border-box;
	position: relative;
}
.navType_one{
	width: 33.33%;
	display: inline-block;
	float: left;
	text-align: center
}
.navType_two{
	width: 33.33%;
	display: inline-block;
	float: left;
	text-align: center
}
.navType_three{
	width: 33.33%;
	display: inline-block;
	float: left;
	text-align: center
}	
.navType_line{
	position: absolute;
    bottom: .03rem;
    left: 0;
    z-index: 1;
    height: .03rem;
    width: .24rem;
    background-color: #FFFFFF!important;
    border-radius: 3px;
	transition-duration: 0.3s;
}
.lists{
	height: 100%;
}
.list{
	width: 100%;
	/* height: calc(100vh - 1.3rem); */
	height: 100%;
	touch-action: pan-y;
	-webkit-overflow-scrolling: touch;
  	overflow: scroll;
  	overflow-x: hidden;
	padding-top:10px
}
.list ul{	
	width: 91.46%;
	margin: 0rem auto;
	height: auto;
}
.list ul li{
	height:1.01rem;
  	width: 100%;
	margin-top:.12rem;
	background-color:#FFFFFF;
	position:relative;
	box-shadow: 0rem 0rem .1rem #d9d5d5;
	/* background-color: #FFFFFF */
}
.list ul li p{
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
.list ul li>a>.content_right{
	position: absolute;
	height:.5rem;
	line-height: .5rem;
	right:.14rem;
	bottom:0rem;
}
.list ul li>a>.content_right img{
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
.style{
	height: 100%;
	color: #333333;
}
.contentTitle{
	width: 100%;
	padding-top: .1rem;
	padding-left: .1rem;
	
}
.contentTitle img{
	width: .17rem;
	height: .17rem;
	margin-top: -.03rem;
}
.contentTitle span{
	font-size: .14rem;
	font-weight: bold;
}
.contnet_left{
	padding-top: .04rem;
	padding-left: .31rem
}
.contnet_left span{
	display: block;
}
.content_right{
	position: absolute;
	right: .15rem;
	bottom: .25rem;
}
.content_right button{
	width: .8rem;
	height: .28rem;
	background-color: #2B77EF;
	border: none;
	border-radius: .14rem;
	color: #FFFFFF;
	font-size: .14rem;
}
.buttonColor{
    color: #333333!important;
    background-color: #EEEEEE!important;
}
.tabTitle{
	margin: .08rem 0rem;
}
.tabTitle span{
	display: block;
}
.newAdd{
	position: fixed;
	height: calc(100% - 1.3rem);
	width: 91.4%;
	left: 0;
	right: 0;
	margin: 0rem auto;
}
.newAddTitle{
	width: 91.4%;
	margin-top: 2.9%;
	margin: 0 auto;
	padding-top: .2rem;
}
.newAddTitle img{
	width: .165rem;
	height: .185rem;
}
.newAddTitle h3{
	margin-left: .05rem;
	width: .45rem;
	height: .21rem;
	display: inline;
}
.Fill {
	width:90%;
	height: 1.59rem;
}
.Fill li{
	border: 1px solid #D8D8D8;
	border-radius: .02rem;
	padding: .12rem .15rem;
	margin-top:.12rem;
	width: 100%;
}
.Fill li span{
	height: .21rem;width: .6rem;

}
.Fill li input{
	border: none;
	float:right;
	text-align: right;
	background: transparent;
	width: 60%
	/* background-color: #F5F5F5; */
}
.submitClass{
	width:2.41rem;
	height: .4rem;
	display:block;margin:0 auto;
	margin-top: .5rem;
	background-color: #2B77EF;
	/* background: linear-gradient(#56AFF8, #2B77EF); */
	border: none;
	border-radius: .2rem;
	color: #FFFFFF;
	font-size: 	.14rem;
}
.bottom{
	margin-top: .1rem
}
</style>

