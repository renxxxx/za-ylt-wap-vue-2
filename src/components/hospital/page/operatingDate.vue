<template>
	<div class="operatingDate">
    <div class="topNav" :style="{'padding-top':$store.state.paddingTop}">
    	<img src="../../../assets/image/shape@2x.png" alt=""  @click="goBackFn" :style="{'padding-top':$store.state.paddingTop}">
    	<h3>{{nowYear}}年</h3>
      <van-swipe :show-indicators='false' ref="Swipe" @change="changeFn" :initial-swipe='indedxOf'>
      <div v-for="(year,inx) in data" :key="inx">
        <van-swipe-item>
          <div class='mounth' v-for="(item,inx) in year.minMounth" @click="mouthFn(nowYear,item.num)" ref='minMounth' :key="inx" @touchstart="startFn" @touchend="endFn">
            <span :class='item.color'>{{item.num}}月</span>
          </div>
        </van-swipe-item>
        <van-swipe-item>
          <div class='mounth' v-for="(_item,num) in year.maxMounth" @click="mouthFn(nowYear,_item.num)" ref='maxMounth' :key="num" @touchstart="startFn" @touchend="endFn">
            <span :class='_item.color'>{{_item.num}}月</span>
          </div>
        </van-swipe-item>
      </div>
    </van-swipe>
    </div>
    <div class="zhangwei" :style="{'padding-top':$store.state.paddingTop}"></div>
    <div class="center" @scroll="handleScroll" ref="center">
      <div class="title" v-for="(data,index) in dataValue" :key="index">
        <span>{{data.time[0]? moment(data.time[0]).format('YYYY-MM-DD'):''}}</span>
        <van-steps direction="vertical" :active="-9999">
          <van-step v-for="(riqi,num) in data.value" :key="num">
            <span class="addTime">{{riqi.addTime? moment(riqi.addTime).format('hh:mm'):''}}</span>
            <p class="riqiP">{{riqi.hospitalUserName}} 上传了 <span>{{riqi.operatingManualSectionName}}</span> </p>
          </van-step>
        </van-steps>
      </div>
    </div>
    <div class="returnTop" @click="$refs.center.scrollTop=0;hospitalReturnTopPage = false;" ref="returnTopRef" v-show="hospitalReturnTopPage">
			<img src="../../../assets/image/returnTop.png" alt />
			<span>顶部</span>
		</div>
  </div>
</template>

<script>
import qs from 'qs';
import moment from 'moment'
export default {
  name: 'operatingDate',
  data () {
    return {
      nowYear:undefined,
      nowMonth:undefined,
      start:0,
      end:0,
      data:[],
      dataValue:[],
      indedxOf:0,
      query:'',
      scrollTop:0,
    	hospitalReturnTopPage:false,
    }
  },
  computed:{
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
      this.riliFn()
      if(this.scrollTop != 0){
        this.$refs.center.scrollTop = this.scrollTop;
      }
		}
	},
  methods: {
    // 滑动一定距离出现返回顶部按钮
		handleScroll() {
			this.scrollTop = this.$refs.center.scrollTop || this.$refs.center.pageYOffset
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
    riliFn(){
      let timestamp = Date.parse(new Date());
      let date = new Date(timestamp);
      this.nowYear = date.getFullYear();
      this.indedxOf = (this.nowYear-1950)*2
      this.nowMonth = date.getMonth()+1;
      for(let i = 1950;i<=2099;i++){
        if(this.nowYear == i){
          this.data.push({
            year:i,
            minMounth:[],
            maxMounth:[]
          })
          for(let num = 1;num<7;num++){
            if(this.nowMonth == num){
              if(this.nowMonth>6){
                this.data[this.nowYear-1950].maxMounth.push({
                  num: num+6,
                  color:'color'
                })
                this.data[this.nowYear-1950].minMounth.push({
                  num: num,
                  color:'noColor'
                })
              }else{
                this.data[this.nowYear-1950].maxMounth.push({
                  num: num+6,
                  color:'noColor'
                })
                this.data[this.nowYear-1950].minMounth.push({
                  num: num,
                  color:'color'
                })
              }
            }else{
              this.data[this.nowYear-1950].minMounth.push({
                num: num,
                color:'noColor'
              })
              this.data[this.nowYear-1950].maxMounth.push({
                num: num+6,
                color:'noColor'
              })
            }
          }
        }else{
          this.data.push({
            year:i,
            minMounth:[],
            maxMounth:[]
          })
          // this.data[i-1937].minMounth='ss'
          for(let num = 1;num<7;num++){
            this.data[i-1950].minMounth.push({
              num: num,
              color:'noColor'
            })
            this.data[i-1950].maxMounth.push({
              num: num+6,
              color:'noColor'
            })
          }
        }

      }
      // console.dir(this.data)
      this.getData(this.nowYear,this.nowMonth)
    },
    changeFn(index) {
      if((this.start-this.end)<0){
        if(index%2){
          --this.nowYear
        }
      }else{
        if(!(index%2)){
          ++this.nowYear
        }
      }
    },
    startFn(e){
      this.start = e.changedTouches[0].pageX;
    },
    endFn(e){
      this.end = e.changedTouches[0].pageX;
    },
    mouthFn(year,mounth){
      this.data=[];
      for(let i = 1950;i<=2100;i++){
        this.data.push({
          year:i,
          minMounth:[],
          maxMounth:[]
        })
        for(let num = 1;num<7;num++){
          this.data[i-1950].minMounth.push({
            num: num,
            color:'noColor'
          })
          this.data[i-1950].maxMounth.push({
            num: num+6,
            color:'noColor'
          })
        }
      }
      this.dataValue = []
      this.getData(year,mounth)
    },
    getData(year,mounth){
      let timeFrom =this.zhuanHuanFn(year,mounth)
      let timeTo =this.zhuanHuanFn(year,mounth+1)-1
      let addTimeFrom = undefined;
      let addTimeTo = undefined;
      this.$axios.get('/hospital/operating-manual/operating-manual-section-tracks?'
      +qs.stringify({"addTimeFrom":timeFrom})+'&'
      +qs.stringify({"addTimeTo":timeTo})
      )
      .then(res => {
        let _dataValue = [];
        if(!res.data.codeMsg){
          for(let i in res.data.data.rows){
            _dataValue.push(res.data.data.rows[i])
            _dataValue[i].addTime = moment(_dataValue[i].addTime).format('YYYY-MM-DD HH:mm:ss')
            _dataValue[i].alterTime = moment(_dataValue[i].alterTime).format('YYYY-MM-DD HH:mm:ss')
          }
        }else{
          this.$toast(res.data.codeMsg)
        }
        if(res.data.data.rows.length){
          let num = 0;
          this.dataValue.push({
            time:undefined,
            value:[]
          })
          // console.dir(_dataValue[0]);
          this.dataValue[num].time = _dataValue[0].addTime.split(' ');
          for(let i = 0;i<_dataValue.length;i++){
            let _time = _dataValue[i].addTime.split(' ');
            if(this.dataValue[num].time[0] == _time[0]){
              
              this.dataValue[num].value.push(_dataValue[i])
            }else{
              ++num;
              this.dataValue[num] = {
                time : undefined,
                value :[]
              }
              this.dataValue[num].time = _dataValue[i].addTime.split(' ');
              this.dataValue[num].value.push(_dataValue[i])
            }
          }
        }
      })
      .catch((err)=>{
      })
    },
    zhuanHuanFn(year,mounth){
      let _time = undefined
      if(mounth<10){
        _time = year.toString()+0+mounth.toString()+'01'
      }else{
        _time = year.toString()+mounth.toString()+'01'
      }
      _time = moment(_time).format('YYYY-MM-DD HH:mm:ss')
      let data = new Date(_time).getTime();
      return data;
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.operatingDate{
  width: 100%;
  height: 100%;
  overflow: hidden;
  background-color: #F5F5F5;
}
.topNav{
	width: 100%;
	height: 1.1rem;
	line-height: .47rem;
	text-align: center;
	position: fixed;
	top:0;
	z-index: 999;
  color: #FFFFFF;
	background-color: #2B77EF;
}
.zhangwei{
	width: 100%;
	height: 1.1rem;
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
.mounth{
  width: 16.66%;
  height: .63rem;
  line-height: .63rem;
  color: #FFFFFF;
  display: inline-block;

  text-align: center;
}
.mounth>span{
  width: .38rem;
  height: .38rem;
  line-height: .38rem;
  display: block;
  border-radius: 50%;
  margin:.125rem auto;
}
.mounth>span:hover{
  background-color: #FFFFFF;
  color: #2B77EF;
}
.color{
  background-color: #FFFFFF;
  color: #2B77EF;
}
>>>.van-swipe{
  background-color: #2B77EF;
}
.center{
  width: 100%;
  height: calc(100% - 1.1rem);
  touch-action: pan-y;
	-webkit-overflow-scrolling: touch;
	overflow: scroll;
	overflow-x: hidden;
}
.title{
  width: 100%;
  margin: .15rem auto;
}
.title>span{
  width: 25.8%;
  height: .25rem;
  line-height: .25rem;
  color: #FFFFFF;
  display: block;
  background-color: #FF932E;
  border-radius: 0rem .5rem .5rem 0rem;
  margin-bottom: .15rem;
  text-align: center;
}
.title>ul{
  width: 100%;
}
.title>ul>li{
  width: 100%;
  height: ;
}
.addTime{
  font-size: .14rem;
  color: #999999;
}
.riqiP{
  font-size: .15rem;
  color: #333333;
}
.riqiP>span{

  color: #2B77EF;
}
.color{
  background-color: #FFFFFF;
  color: #2B77EF;
}
.noColor{
  color: #FFFFFF;
  background-color: transparent
}
>>>.van-swipe-item{
  float: left;
  height: auto;
}
</style>
