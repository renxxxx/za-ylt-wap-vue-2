<template>
	<div class="expertsIntroduction">
		<div class="topNav" :style="{'padding-top':$store.state.paddingTop}">
			<div class="leftImg" @click="goBackFn"  id="navback">
				<img src="../../../assets/image/shape@3x.png" alt="">
			</div>
			<div class="centerTitle">
				<h3>专家介绍</h3>
			</div>
			<div class="right"></div>
		</div>
		<div class="zhangwei" :style="{'padding-top':$store.state.paddingTop}"></div>
		<div class="content" :style="{height: 'calc(100% - '+ (parseInt($store.state.paddingTop.replace('px',''))+47+'px)')}" @scroll="handleScroll" ref="content">
			<van-list v-model="loading" :finished="finished" finished-text="没有更多了" @load="onLoad">
				<ul>
					<li v-for="(item,inx) in doctor" :key='inx' @click="showContent(item)">
						<div class="contentImg">
							<img :src="item.headimg" alt="">
						</div>

						<div class="contentLists">
							<h4>{{item.name}}</h4>
							<!-- <span>{{item.hosptialName}}</span> -->
							<span class="xia line-1">{{item.jobTitles}}</span>
							<div class="duanluo" >
								<p ref='showP'>{{item.intro}}</p>
								<!-- <img :src="downImg" alt="" ref='showimg'> -->
							</div>
						</div>
						<!-- <hr> -->
					</li>
				</ul>
			</van-list>
		</div>
		<div class="returnTop" @click="$refs.content.scrollTop=0;hospitalReturnTopPage = false;" ref="returnTopRef" v-show="hospitalReturnTopPage">
			<img src="../../../assets/image/returnTop.png" alt />
			<span>顶部</span>
		</div>
	</div>
</template>

<script>
import qs from 'qs';
export default {
	name: 'expertsIntroduction',
	data () {
		return {
			doctor:[],
			downImg : require('../../../assets/image/down@2x.png'),
			clickNum: 0 ,
			loading: false,
			finished: false,
			page: 0,
			query:'',
			scrollTop:0,
    		hospitalReturnTopPage:false,
		}
	},
	components:{
	},
	computed:{
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
			this.onLoad()
		}
		if(this.scrollTop != 0){
			this.$refs.content.scrollTop = this.scrollTop;
		}
	},
	methods: {
		// 滑动一定距离出现返回顶部按钮
		handleScroll() {
			this.scrollTop = this.$refs.content.scrollTop || this.$refs.content.pageYOffset
			if (this.scrollTop > 800) {
				this.hospitalReturnTopPage = true;
			} else {
				this.hospitalReturnTopPage = false;
			}
		},
		//回退方法
		goBackFn(){
			this.$router.back()
		},
		//显示内容
		showContent(item){
			this.$router.push({path:'/hospital/hospital_expertsIntroductionDeatlis',query:{data:JSON.stringify(item),time: new Date().getTime().toString()}})
			// localStorage.setItem('hospital_expertsIntroductionDeatlisData',JSON.stringify(item))
			// this.clickNum++;
			// if(this.clickNum % 2 == 0){
			// 	this.$refs.showP[inx].style.webkitLineClamp = '9999'
			// 	this.$refs.showimg[inx].src=require('../../../assets/image/up-1@2x.png')
			// }else{
			// 	this.$refs.showP[inx].style.webkitLineClamp = '2'
			// 	this.$refs.showimg[inx].src=require('../../../assets/image/down@2x.png')
			// }
		},
		onLoad(){
			++this.page;
			this.getData();
		},
		getData(){
			this.$axios.post('/c2/doctor/items',qs.stringify({
				hospitalId : this.$store.state.hospital.login.hospital.hospitalId,
				pn: this.page,
				ps: 10
			}))
			.then(_d => {
				if(_d.data.data.items.length != 0){
				for(let i in _d.data.data.items){
					this.doctor.push({
					name : _d.data.data.items[i].name,
					hosptialName : _d.data.data.items[i].hosptialName,
					intro : _d.data.data.items[i].intro,
					jobTitles : _d.data.data.items[i].jobTitles,
					headimg : _d.data.data.items[i].headimg,
					addTime : _d.data.data.items[i].addTime,
					alterTime : _d.data.data.items[i].alterTime,
					})
				}
				this.loading = false;
				}else {
					this.loading = false;
					this.finished = true;
				}
			})
			.catch((err)=>{})
		}
	},
}
</script>

<style scoped>
.expertsIntroduction{
	width: 100%;
	height: 100%;
	background-color: #F5F5F5;
	/* background-color: #FFFFFF; */
}
.topNav{
	width: 100%;
	height: .47rem;
	line-height: .47rem;
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
.content{
	width: 100%;
	height: calc(100% - .47rem);
	touch-action: pan-y;
	-webkit-overflow-scrolling: touch;
	overflow: scroll;
	overflow-x: hidden;
}
.content ul {
	width: 100%;
}
.content ul li {
	width: 100%;
	height: auto;
	/* margin: .12rem auto; */
	background-color: #FFFFFF;
	position: relative;
	/* padding-bottom: .18rem; */
	margin-top: 12px;
}
.contentImg{
	width: .98rem;
	height: 123px;
	box-sizing: border-box;
	display: inline-block;
}
.contentImg img{
	width: .65rem;
	height: .65rem;
	margin : 22px .15rem 36px .18rem;
	float: left;
	border-radius: 50%;
	object-fit: cover;
}
.contentLists{
	width: calc(100% - .98rem);
	height: 100%;
	display: block;
	padding-top: 18px;
	box-sizing: border-box;
	padding-right: .16rem;
	float: right;
	box-sizing: border-box;
}
.contentLists h4{
	font-size: .15rem;
	font-weight: bold;
	color: #333333;
}
.contentLists>span:nth-child(2){
	padding:4px 0px;
	margin-right: .1rem;
	font-size: .12rem;
	height: 15px;
}     
.xia{
	/* margin-left: .1rem; */
	position: relative;
}
.xia:before{
	content: "";
	width: .01rem;
	height: .06rem;
	background-color: #D8D8D8;
	position: absolute;
	top: .11rem;
	left: -.11rem;
}
.duanluo{
	position: relative;
	height: 100%;
	width: 100%;
}
.duanluo>p{
	min-height: .35rem;
	width: 100%;
	padding-top: .04rem;
	color: #999999;
	display: -webkit-box;
	overflow: hidden;
	text-overflow: ellipsis;
	word-wrap: break-word;
	-webkit-line-clamp: 2;
	-webkit-box-orient: vertical;
}
.duanluo>img{
	width: .12rem;
	height: .07rem;
	position: absolute;
	right: -.33rem;
	bottom: .11rem;
}
/* hr{
	width: 91%;
	border-top:1px solid #D8D8D8;
}
.content ul li:last-child hr{
	display: none;
} */
</style>
