<template>
	<div class="hospital">
		<div class="topNav" :style="{'padding-top':$store.state.paddingTop}">
			<div class="leftImg" @click="goBackFn">
				<img src="../../../assets/image/shape@3x.png" alt="">
			</div>
			<div class="hospital_search">
				<input type="search" v-model="kw" placeholder="搜索文章" @keyup.enter="searchFn" v-focus='true'>
				<img src="../../../assets/image/sousuo@2x.png" alt="">
			</div>
			<div class="clinic_buttton" @click="searchFn">
				<button>搜索</button>
			</div>
		</div>
		<div style="height:.47rem" :style="{'padding-top':$store.state.paddingTop}"> </div>
		<div class="article" @scroll="handleScroll" ref="article">
			<!-- <van-pull-refresh v-model="pullingDown" @refresh="afterPullDown" > -->
				<ul :model="article">
					<van-list  v-model="loading" :finished="finished" finished-text="没有更多了"  @check="onLoad">
						<li v-for="(items,inx) in article" :key="inx" @click="$router.push({path:'/outpatient/outpatient_caseDetails',query:{itemId : items.itemId,data: 1,time: new Date().getTime()}})">
							<!-- <router-link :to="{path : '/outpatient/outpatient_caseDetails' ,query : {itemId : items.itemId,data: 1,}}"> -->
								<div class="article_left" :style="{width:items.img?'60.1%':'100%'}">
									<p>{{items.content}}</p>
									<div class="article_leftTime">
										<img src="../../../assets/image/time@2x.png" alt="">
										<span>{{moment(items.time).format('YYYY-MM-DD HH:mm')}}</span>
									</div>
								</div>
								<div class="article_right">
									<img :src=items.img alt="">
								</div>
							<!-- </router-link> -->
						</li>
					</van-list>
				</ul>
			<!-- </van-pull-refresh> -->
		</div>
		<div class="returnTop" @click="$refs.article.scrollTop=0;hospitalReturnTopPage = false;" ref="returnTopRef" v-show="hospitalReturnTopPage">
			<img src="../../../assets/image/returnTop.png" alt />
			<span>顶部</span>
		</div>
	</div>
</template>

<script>
import qs from 'qs';
export default {
	name: 'hospital',
	data () {
		return {
			name: 'hospital',
			article:[],
			loading: false,
			finished: false,
			page:1,
			kw: '',
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
		goBackFn(){
		this.$router.back()
		},
		getdata(_data){
			this.$axios.post('/c2/article/items',qs.stringify(_data))
			.then(res => {
				if(res.data.data.items.length != 0){
					for(let i in res.data.data.items){
					// 
					if(res.data.data.items[i]){
						this.article.push({
							content:res.data.data.items[i].title,
							img: res.data.data.items[i].cover,
							time:res.data.data.items[i].alterTime,
							itemId: res.data.data.items[i].itemId,
						})
					}else{
					}
				}
				this.loading = false;
				this.finished = true;
				}else{
					this.loading = false;
					this.finished = true;
				}
			})
			.catch((err)=>{
				
			})
		},
		onLoad(){
			this.getdata({
				hospitalId : this.$store.state.outpatient.login.hospital.hospitalId,
				pn : this.page,
				ps : 10
			})
			this.page++;
		},
		searchFn(){
			this.article = [];
			this.getdata({
				kw : this.kw,
				hospitalId : this.$store.state.outpatient.login.hospital.hospitalId,
			})
		}
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.hospital{
	width: 100%;
	height: 100%;
	overflow: hidden;
}
.topNav{
	width: 100%;
	height: .47rem;
	position: fixed;
	background-color: #FFFFFF;
	z-index: 9
}
.leftImg{
	width: 10%;
	height: .47rem;
	float:left;
  position: relative;
}
.leftImg img{
	width: .09rem;
	height: .15rem;
	position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  margin: auto;
}
.hospital_search{
	float:left;
	width: 75%;
	position: relative;
}
.hospital_search input{
	margin: .08rem 0rem 0rem 0rem;
	height:.34rem;
	width: 93%;
	border: none;
	border-radius: .33rem;
	padding-left: 11.6%;
	background-color: rgba(0, 0, 0, 0.05);
}
.hospital_search input[type=search]::-webkit-search-cancel-button{
  padding-right: 5%;
}
.hospital_search img{
	width: .14rem;
	height: .15rem;
	position: absolute;
	top: .18rem;
	left: 4.8%;
}
.clinic_buttton{
	float: left;
	margin-top: .125rem;
	margin-left: -.05rem;
}
.clinic_buttton button{
	color: #FFFFFF;
	background-color: #2B77EF;
	border-radius: .15rem;
	border: none;
	height: .28rem;
	width: .5rem;
	font-size: .12rem;
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
	width: 91.5%;
	margin: 0rem auto;
	margin-top: .2rem;
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
	display: -webkit-box;
	overflow: hidden;
	text-overflow: ellipsis;
	word-wrap: break-word;
	-webkit-line-clamp: 2;
	-webkit-box-orient: vertical;
	height: .42rem;
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
	object-fit: cover;
}
</style>
