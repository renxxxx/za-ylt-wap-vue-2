<template>
	<div class="operatingManualList">
		<div class="topNav" :style="{'padding-top':$store.state.paddingTop}">
			<div class="leftImg" @click="goBackFn"  id="navback">
				<img src="../../../assets/image/shape@3x.png" alt="" id="navback">
			</div>
			<div class="centerTitle">
				<h3>{{this.$route.query.name}}</h3>
			</div>
			<div class="right"></div>
		</div>
    <div class="zhangwei" :style="{'padding-top':$store.state.paddingTop}"></div>
	<div style="margin-top: .2rem;">
      <div v-for="(item,inx) in operatingManualList" :key="inx" @click="nextPageFn(item)">
          <van-cell is-link>
            <template>
              <span class="custom-title">{{item.name}}</span>
            </template>
          </van-cell>
      </div>
    </div>
  </div>
</template>

<script>
import qs from 'qs';
export default {
  name: 'operatingManualList',
  data () {
    return {
      activeNames: ['1'],
      operatingManualList : [],
      num:0,
      yesNum:0,
			query:''
    }
  },
  computed:{
  },
  created () {
  },
  mounted () {
  },
	activated() {
		if(this.query != JSON.stringify(this.$route.query)){
      Object.assign(this.$data, this.$options.data());
			this.query = JSON.stringify(this.$route.query);
			if(window.plus){
				plus.navigator.setStatusBarStyle("dark")
			}
			this.getData()
		}
	},
  methods: {
    //回退方法
    goBackFn(){
    	this.$router.back()
    },
    nextPageFn(item){
      console.dir(item.lowerCount)
      if(item.lowerCount){
        console.dir(item.operatingManualSectionId)

        this.$router.push({name:'hospital_operatingManualListSix',query:{name:item.name,operatingManualId:this.$route.query.operatingManualId,operatingManualSectionId : item.operatingManualSectionId,time: new Date().getTime().toString()}})
      }else{
        this.$router.push({name:'hospital_operatingManualListDetails',query:{name:item.name,operatingManualId:this.$route.query.operatingManualId,operatingManualSectionId : item.operatingManualSectionId,time: new Date().getTime().toString()}})
      }
    },
    getData(){
      this.$axios.get('/hospital/operating-manual/operating-manual-sections?'
        +qs.stringify({"operatingManualId":this.$route.query.operatingManualId})+'&'
        +qs.stringify({"upperId":this.$route.query.operatingManualSectionId})
        )
      .then(res => {
        if(!res.data.codeMsg){
          for(let i in res.data.data.rows){
            this.operatingManualList.push(res.data.data.rows[i])
          }
        }else{
          this.$toast(res.data.codeMsg)
        }
      })
      .catch((err)=>{
      	
      })
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.operatingManualList{
  width: 100%;
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
>>>.van-collapse{
  margin-top: .12rem;
}
>>>.van-collapse-item__content {
    padding: 0rem;
    /* padding-left: .16rem; */
}
.manualList{
  width: 100%;
  height: .44rem;
  line-height: .44rem;
  border-bottom: 1px solid #ebedf0;
  padding-left: .16rem;
  position: relative;
  font-size: .14rem;
}
.manualList>img{
  width: .09rem;
  position: absolute;
  right: .34rem;
  top: 0;
  bottom: 0;
  margin: auto 0rem;
}
.doColor{
  color: #2B77EF;
}
.title>p{
  float: right;
}
</style>
