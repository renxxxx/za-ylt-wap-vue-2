<template>
	<div class="case">
		<div class="topNav" :style="{'padding-top':$store.state.paddingTop}">
			<img src="../../../../assets/image/shape@3x.png" alt=""  @click="goBackFn"  id="navback" :style="{'padding-top':$store.state.paddingTop}">
			<h3>运营文章</h3>
		</div>
		<div class="zhangwei"></div>
		<div class="article" :style="{'padding-top':$store.state.paddingTop}" @scroll="handleScroll" ref="article">
			<!-- <van-pull-refresh v-model="pullingDown" @refresh="afterPullDown"> -->
				<van-list v-model="loading" :finished="finished" finished-text="没有更多了" @load="onLoad">
					<ul>
						<li v-for="(items,inx) in article" :key="inx">
							<!-- <router-link :to="{path : '/hospital/hospital_caseDetails' ,query : {itemId : items.itemId,data: 4,}}"> -->
							<div class="article_left" :style="{width:items.img?'60.1%':'100%'}" @click="$router.push({path:'/promoters/promoters_caseDetails',query:{itemId : items.itemId,data: 1,time: new Date().getTime().toString()}})">
								<p>{{items.content}}</p>
								<div class="article_leftTime">
								<img src="../../../../assets/image/time@2x.png" alt="">
								<span>{{moment(items.time).format('YYYY-MM-DD HH:mm')}}</span>
								</div>
							</div>
							<div v-if="items.img"  class="article_right">
								<img :src=items.img alt="">
							</div>
							<!-- </router-link> -->
						</li>
					</ul>
				</van-list>
			<!-- </van-pull-refresh> -->
		</div>
		<div class="returnTop" @click="$refs.article.scrollTop=0;hospitalReturnTopPage = false;" ref="returnTopRef" v-show="hospitalReturnTopPage">
			<img src="../../../../assets/image/returnTop.png" alt />
			<span>顶部</span>
		</div>
	</div>
</template>

<script>
import qs from 'qs';
export default {
	name: 'case',
	data () {
		return {
			article:[],
			loading: false,
			finished: false,
			page: 0,
			query:'',
			scrollTop:0,
			hospitalReturnTopPage:false,
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
			this.initData()
			this.query = JSON.stringify(this.$route.query);
			if(window.plus){
				//plus.navigator.setStatusBarBackground("#ffffff");
				plus.navigator.setStatusBarStyle("dark")
			}
		}
		if(this.scrollTop != 0){
			this.$refs.article.scrollTop = this.scrollTop;
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
			this.scrollTop = this.$refs.article.scrollTop || this.$refs.article.pageYOffset
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
		onLoad(){
			++this.page;
			this.getData();
		},
		getData(){
			this.$axios.post('/c2/article/items',qs.stringify({
				hospitalId : this.$store.state.hospital.login.hospital.hospitalId,
				pn: this.page,
				ps: 10
			}))
			.then(res => {
				if(res.data.data.items.length != 0){
					for(let i in res.data.data.items){
						// 
						if(res.data.data.items[i]){
							this.article.push({
								content:res.data.data.items[i].title,
								img: res.data.data.items[i].cover,
								time:res.data.data.items[i].alterTime,
								itemId : res.data.data.items[i].itemId,
							})
						}
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
.case{
	width: 100%;
	height: 100%;
	overflow: hidden;
}
.topNav{
	width: 100%;
	height: .47rem;
	line-height: .47rem;
	text-align: center;
	position: fixed;
	top:0;
	z-index: 999;
	background-color: #FFFFFF;
}
.zhangwei{
	width: 100%;
	height: .47rem;
}
.topNav img{
	width: .09rem;
	height: .15rem;
	position: absolute;
	left: .16rem;
	top:.16rem;
}
.topNav h3{
	font-size: .16rem;
	font-weight: bold;
}
.article{
	width: 100%;
	height: calc(100% - .47rem);
	touch-action: pan-y;
	-webkit-overflow-scrolling: touch;
	overflow: scroll;
	overflow-x: hidden;
}
.article ul{
	margin-top: .2rem;
	margin: 0rem auto;
	width: 91.5%;
}
.article ul li {
	width: 100%;
	height: .97rem;
	border-bottom:1px solid #D8D8D8 ;
	margin: .12rem 0;
}
.article ul li:last-child{
	border: none;
}
.article_left{
	float: left;
	width: 60.1%;
}
.article_left p{
	font-size: .14rem;
	font-weight: bold;
	height: .42rem;
	display: -webkit-box;
	overflow: hidden;
	text-overflow: ellipsis;
	word-wrap: break-word;
	-webkit-line-clamp: 2;
	-webkit-box-orient: vertical;
	height: 0.42rem;
}
.article_leftTime{
	margin-top: .23rem;
	height: .16rem;
	line-height: .16rem;
	position: relative;
}
.article_leftTime img{
	position: absolute;
	top:.02rem;
	width: .11rem;
	height: .11rem;

}
.article_leftTime span{
	margin-left: .17rem;
}
.article_right{
	float: left;
	margin-left: 7.8%;
	width: 32.1%;
}
.article_right img{
	width: 1.08rem;
	height: .85rem;
}
</style>
