<template>
	<div class="task">
		<!-- <van-pull-refresh v-model="pullingDown" @refresh="afterPullDown" > -->
			<div class="topNav" :style="{'padding-top':$store.state.paddingTop}">
				<div class="leftImg" @click="goBackFn"  id="navback">
					<img src="../../../assets/image/shape@3x.png" alt="">
				</div>
				<div class="centerTitle">
					<h3>任务管理</h3>
				</div>
				<div class="right"></div>
			</div>
			<div class="zhangwei" :style="{'padding-top':$store.state.paddingTop}"></div>
			<div class="taskList" :style="{height: 'calc(100% - '+ (parseInt($store.state.paddingTop.replace('px',''))+176+'px)')}">
				<div>
					<!-- <van-pull-refresh v-model="pullingDown" @refresh="afterPullDown" style="ovflow:hidden"> -->
				<h3>首次收益</h3>
				<ul>
					<li v-for="(item,inx) in task.one" :key='inx'>
						<input type="checkbox" class="input_check" :checked="item.checked" @change="change($event,item,inx)"/>
						<!-- <router-link :to="{path : '/hospital/hospital_taskManagementDetails' ,query : {item : JSON.stringify(item),show : false,}}"> -->
						<span @click="$router.push({path:'/hospital/hospital_taskManagementDetails',query:{item : JSON.stringify(item),show : false,time: new Date().getTime().toString()}})">{{item.name}}</span>
						<!-- </router-link> -->
					</li>
				</ul>
				<h3>每日收益</h3>
				<ul :style="{'margin-bottom':$store.state.paddingTop}">
					<li v-for="(item,inx) in task.no" :key='inx' >
						<input type="checkbox" class="input_check" :checked="item.checked" @change="change($event,item,inx)"/>
						<!-- <router-link :to="{path : '/hospital/hospital_taskManagementDetails' ,query : {item : JSON.stringify(item),show : true,}}"> -->
						<span @click="$router.push({path:'/hospital/hospital_taskManagementDetails',query:{item : JSON.stringify(item),show : true,time: new Date().getTime().toString()}})">{{item.name}}</span>
						<!-- </router-link> -->
					</li>
				</ul>
				<!-- </van-pull-refresh> -->
				</div>
			</div>
		<!-- </van-pull-refresh> -->
	</div>
</template>

<script>
import qs from 'qs';
export default {
	name: 'search',
	data () {
		return {
			checked: true,
      //医院端用户的任务管理参数
			task:{
				one:[],
				no:[],
			},
			query:'',
			pullingDown:false,
			pullingDown: false,
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
				//plus.navigator.setStatusBarBackground("#ffffff");
				plus.navigator.setStatusBarStyle("dark")
			}
			// if(this.task.one.length == 0 || this.task.no.length == 0){
			// 	this.getData();
			// }
		}
		Object.assign(this.$data, this.$options.data());
		this.getData()
	},
	methods: {
		afterPullDown() {
			//下拉刷新
		  setTimeout(() => {
			this.pullingDown = false;
			 this.initData();
		  }, 500);
		},
		initData() {
			let thisVue = this
			if(this.$route.meta.auth && !this.$store.state.hospital.login)
			this.$toast({message:'请登录',onClose:function(){
				thisVue.$router.replace({ path : '/hospital/hospitalLogin',query:{time:1}});
			}})
		  Object.assign(this.$data, this.$options.data());
		  this.getData();
		},
		// 返回上一级
		goBackFn(){
			this.$router.back()
		},
		getData(){
			this.$axios.post('/c2/task/tasks',qs.stringify({
					hospitalId : this.$store.state.hospital.login.hospital.hospitalId,
				}))
				.then(res => {
					for(let i in res.data.data.items){
						if(res.data.data.items[i].oneTimeIs  == 1){
							if(res.data.data.items[i].issueIs){
								this.task.one.push({
									name : res.data.data.items[i].name,
									taskId : res.data.data.items[i].taskId,
									oneTimeIs : res.data.data.items[i].oneTimeIs,
									checked : true,
									exchangePointUpperPerDay : res.data.data.items[i].exchangePointUpperPerDay,
									exchangePoint : res.data.data.items[i].exchangePoint,
									everyDayIs : res.data.data.items[i].everyDayIs,
									intro : res.data.data.items[i].intro,
									taskId : res.data.data.items[i].taskId,
								})
							}else{
								this.task.one.push({
									name : res.data.data.items[i].name,
									taskId : res.data.data.items[i].taskId,
									oneTimeIs : res.data.data.items[i].oneTimeIs,
									checked : false,
									exchangePointUpperPerDay : res.data.data.items[i].exchangePointUpperPerDay,
									exchangePoint : res.data.data.items[i].exchangePoint,
									everyDayIs : res.data.data.items[i].everyDayIs,
									intro : res.data.data.items[i].intro,
									taskId : res.data.data.items[i].taskId,
								})
							}
			
						}else{
							if(res.data.data.items[i].issueIs){
								this.task.no.push({
									name : res.data.data.items[i].name,
									taskId : res.data.data.items[i].taskId,
									oneTimeIs : res.data.data.items[i].oneTimeIs,
									checked : true,
									exchangePointUpperPerDay : res.data.data.items[i].exchangePointUpperPerDay,
									exchangePoint : res.data.data.items[i].exchangePoint,
									everyDayIs : res.data.data.items[i].everyDayIs,
									intro : res.data.data.items[i].intro,
									taskId : res.data.data.items[i].taskId,
								})
							}else{
								this.task.no.push({
									name : res.data.data.items[i].name,
									taskId : res.data.data.items[i].taskId,
									oneTimeIs : res.data.data.items[i].oneTimeIs,
									checked : false,
									exchangePointUpperPerDay : res.data.data.items[i].exchangePointUpperPerDay,
									exchangePoint : res.data.data.items[i].exchangePoint,
									everyDayIs : res.data.data.items[i].everyDayIs,
									intro : res.data.data.items[i].intro,
									taskId : res.data.data.items[i].taskId,
								})
							}
						}
					}
				})
				.catch((err)=>{})
		},
		change(_value,_item,inx){
			if(_item.checked){
				this.$axios.post('/c2/task/taskunissue',qs.stringify({
					hospitalId : this.$store.state.hospital.login.hospital.hospitalId,
					taskId : _item.taskId
				}))
				.then(res =>{
					this.$toast.success('操作成功')
				})
				.catch((err)=>{})
			}else{
				debugger
				this.$axios.post('/c2/task/taskissue',qs.stringify({
					hospitalId : this.$store.state.hospital.login.hospital.hospitalId,
					taskId : _item.taskId,
				})).then(res =>{
					res.data.codeMsg? this.$toast(res.data.codeMsg): this.$toast.success('操作成功')
					
				}).catch(err =>{})
			}
		},
	},
}
</script>

<style scoped>
.task{
	width: 100%;
	height: 100%;
	overflow: hidden;
	/* background-color: #F5F5F5; */
	background-color: #FFFFFF;
}
.topNav{
	width: 100%;
	height: 1.76rem;
	background: url('../../../assets/image/tu1.png')  center no-repeat,linear-gradient(#FDFDFD, #FBFBFB) ;
	background-size: 1.84rem 1.29rem;
	/* margin-bottom: .15rem; */
	position: fixed;
	top: 0;
	z-index: 999;
}
.zhangwei{
	width: 100%;
	height: 1.76rem;
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
	padding-top: .15rem
}
.right{
	width: 10%;
	height: .47rem;
	line-height: .47rem;
	float:left;
}
.taskList{
	width: 100%;
	height: calc(100% - 1.76rem);
	touch-action: pan-y;
	-webkit-overflow-scrolling: touch;
	overflow: scroll;
	/* overflow-x: hidden; */
}
.taskList h3{
	color: #999999;
	/* margin: 0rem .16rem; */
	position: relative;
	margin-left: .23rem;
}
.taskList h3:before{
	content: "";
	display: inline-block;
	width: .04rem;
	height: .11rem;
	background-color: #2B77EF;
	-webkit-box-sizing: border-box;
	box-sizing: border-box;
	/* border-radius: 3px; */
	position: absolute;
	top: .035rem;
	left: -.09rem;
}
.taskList ul{
	width: 100%;
	/* margin: .15rem auto; */
	background-color: #FFFFFF;
	margin-bottom: .18rem;
}
.taskList ul:last-child li:last-child{
	border: none;
	/* margin-bottom: .35rem; */
}
.taskList ul>li{
	width: 92%;
	height: .49rem;
	color: #000000;
	line-height: .49rem;
	font-size: .15rem;
	margin: 0rem auto;
	border-bottom: 1px solid #DDDDDD;
}
.taskList ul>li>a>span{
	float: left;
	width: 80%;
}

.taskList ul>li input[type=checkbox] {
  width: .22rem;
  height: .22rem;
  background: url('../../../assets/image/Notselected@2x.png') center no-repeat;
  -webkit-appearance: none;
  background-size: .22rem .22rem;
  border: none;
  outline: 0 !important;
  /* color: #d8d8d8; */
  position: relative;
  margin-top: .14rem;
  margin-right: .15rem;
  padding: 0!important;
  float: left;
}

.taskList ul>li input[type=checkbox]:checked:before{
  content: "";
  display:inline-block;
  width: .22rem;
  height: .22rem;
  background: url('../../../assets/image/Select@2x.png')center no-repeat;
  background-size: .22rem .22rem;
  box-sizing:border-box;
  position: absolute;
  top: 0;
  left: 0rem;
}
/* >>>.van-pull-refresh{
	height: 100%;
} */
</style>
