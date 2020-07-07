<template>
	<div class="imageType">
		<ul>
			<li v-for="(item,inx) in type" :key='inx'>
				<router-link :to="{path : '/hospital/hospital_typeDetails' ,query : {item : item.itemId,}}">
					<img :src="item.url" alt="">
					<span>{{item.name}}</span>
				</router-link>
			</li>
		</ul>
	</div>
</template>

<script>
import qs from 'qs';
export default {
  name: 'hospital_About',
  data () {
    return {
		type:[],
		query:'',
		keepAlive:false
    }
  },
  computed:{
  },
  created () {
		
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
		  this.type =[]
  		this.getData()
  	}
  },
  methods: {
	getData(){
		this.$axios.post('/c2/office/items',qs.stringify({
  					hospitalId : this.$store.state.hospital.login.hospital.hospitalId,
			  }))
  			.then(_d => {
  				for(let i in _d.data.data.items){
  					this.type.push({
  						name: _d.data.data.items[i].name,
  						url : _d.data.data.items[i].cover,
  						itemId : _d.data.data.items[i].itemId,
  					})
  				}
  			})
  			.catch((err)=>{
  				this.$toast(err)
  			})
	}
  },
}
</script>

<style scoped>
.imageType{
	width: 100%;
	height: calc(100% - .76rem);
	overflow: scroll;
}
.content ul{
	width: 97.2%;
	margin: 0rem auto;
}
.content ul li{
	width: 25%;
	height: .66rem;
	text-align: center;
	float: left;
	margin-bottom: .27rem;
}
.content ul li img{
	width: .29rem;
	height: .33rem;
	margin-bottom: .05rem;
	object-fit: cover;
}
.content ul li span{
	display: block;
}
</style>
