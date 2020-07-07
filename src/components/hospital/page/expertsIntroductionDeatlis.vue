<template>
    <div class="expertsIntroductionDeatlis">
        <div class="topNav" :style="{'padding-top':$store.state.paddingTop}">
			<div class="leftImg" @click="goBackFn"  id="navback">
				<img src="../../../assets/image/shape@3x.png" alt="">
			</div>
			<div class="centerTitle">
				<h3>专家介绍</h3>
			</div>
			<div class="right"></div>
		</div>
		<div class="zhangwei"></div>
        <div class="content"> 
            <div class="content_title">
                <div class="content_title_img">
                    <img :src="data.headimg" alt="">
                </div>
                <div class="content_title_message">
                    <h4>{{data.name}}</h4>
                    <h5>{{data.hosptialName}}</h5>
                    <p>{{data.addTime? moment(data.addTime).format('YYYY-MM-DD hh:mm'):''}}</p>
                </div>
            </div>
            <div class="content_position"> 
                <p>{{data.jobTitles}}</p>
            </div>
            <div class="content_paragraph">
                {{data.intro}}
            </div>
        </div>
    </div>
</template>

<script>
import qs from 'qs';
export default {
	name: 'expertsIntroductionDeatlis',
	data () {
		return {
			data:{}
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
            // this.data = JSON.parse(localStorage.getItem('hospital_expertsIntroductionDeatlisData'))
            this.data = JSON.parse(this.$route.query.data)
            localStorage.clear('hospital_expertsIntroductionDeatlisData')
            console.dir(this.data)
			this.query = JSON.stringify(this.$route.query);
			if(window.plus){
				//plus.navigator.setStatusBarBackground("#ffffff");
				plus.navigator.setStatusBarStyle("dark")
			}
		}
	},
    methods: {
        goBackFn(){
            this.$router.back()
        }
    }
    
}
</script>


<style scoped>
.expertsIntroductionDeatlis{
	width: 100%;
	height: 100%;
	/* background-color: #F5F5F5; */
	background-color: #F5F5F5;
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
    margin-top: 12px;
    background-color: #FFFFFF;
    height: calc(100% - 59px);
    width: 100%;
    box-sizing: border-box;
}
.content_title{
    height: auto;
    width: 100%;
}
.content_title_img{
    width: .88rem;
    height: .97rem;
    position: relative;
    display: inline-block;
}
.content_title_img>img{
    width: .65rem;
    height: .65rem;
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    margin: auto;
    border-radius: 50%
}
.content_title_message{
    float: right;
    width: calc(100% - .88rem);
    height: .97rem;
    box-sizing: border-box;
}
.content_title_message>h4{
    font-size: .16rem;
    font-weight: 500;
    padding-top: 17px
}
.content_title_message>h5{
    font-size: .14rem;
    padding: 1px 0px;
}
.content_title_message>p{
    font-size: .14rem;
    color: #00000072;
}
.content_position{
    padding: 0px .16rem;
    box-sizing: border-box;
    color: #333333
}
.content_paragraph{
    margin-top: 15px;
    padding: 0px .16rem;
    box-sizing: border-box;
    color: #999999
}
</style>