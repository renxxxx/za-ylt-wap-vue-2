<template>
	<div class="_photo" >
		<!-- <van-image-preview
			v-model="enlarge"
			:images="imgUrl"
			:start-position='photoNum'
			@change="onChange"
			lazy-load = true
		>
		@close="backFn"
		  <template v-slot:index>第{{ photoPage+1 }}页</template>
		</van-image-preview> -->
	</div>
</template>

<script>
import axios from 'axios'
import {mapActions,mapGetters} from 'vuex'
import qs from 'qs';
import { ImagePreview } from 'vant';
export default {
	name: '',
	data () {
		return {
			enlarge : true,
			imgUrl:[],
			photoNum : 0,
			photoPage : 0,
			inx: 0,
			query:''
		}
	},
	computed:{

	},
	components:{

	},
	created () {
		
		// 
	},
	
	mounted () {
	},
	activated() {
		if(this.query != JSON.stringify(this.$route.query)){
			Object.assign(this.$data, this.$options.data());
			this.query = JSON.stringify(this.$route.query);
			if(window.plus){
				//plus.navigator.setStatusBarBackground("#ffffff");
				plus.navigator.setStatusBarStyle("dark")
			}
			this.imgUrl = []
			console.log(typeof this.$route.query.imgUrl)
			if(typeof this.$route.query.imgUrl == 'string'){
				this.imgUrl.push(this.$route.query.imgUrl)
			}else{
				for(let i in this.$route.query.imgUrl){
					this.imgUrl.push(this.$route.query.imgUrl[i]);
					this.inx = i
				}
			}
			// if(typeof this.$route.query.imgUrl == 'string')
			
			
			// this.inx = this.$route.query.inx;
		this.enlarge = this.$route.query.data;
			let _this = this
			ImagePreview({
				images: this.imgUrl,
				asyncClose: false,
				startPosition: this.$route.query.inx? this.$route.query.inx : 0,
				onClose(){
					_this.$router.back()
				}
			});
		}
	},
	methods: {
		// backFn(){
		// 	debugger

		// 	//this.enlarge = false;
		// 	this.$router.back()
		// 	
		// },
		// enlargeFn(_value){
		// 	this.photoNum = _value;
		// 	
		// 	this.enlarge = true;
		// },
		// onChange(_value){
		// 	this.photoPage = _value;
		// 	
		// },
	},
}
</script>

<style scoped>

</style>
