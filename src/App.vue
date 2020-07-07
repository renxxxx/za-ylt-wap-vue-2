<template>
  <div id="app" ref='appRef'>
    <div class="xiantiao" v-if="$store.state.loading_lineNum" :style="{'top':$store.state.paddingTop}"></div>
   <keep-alive>
      <router-view ></router-view>
  </keep-alive>
  </div>
</template>

<script>
export default {
  name: 'App',
  watch:{
     $route(to,from){
         if(!to.query.time || !from.query.time || to.query.transition=='def')
            this.direction = "";
         else if(to.query.time > from.query.time)
             this.direction = "slide-left";
         else
             this.direction = "";
     }
  },
 
  beforeCreate(){
    debugger
  },
  created(){
    if(!this.cookieOn()){
        this.$dialog.confirm({
              message: '您的浏览器限制了第三方Cookie, 这将影响您正常登录, 您可以更改浏览器的隐私设置, 解除限制后重试.'
            })
    }
  },
  beforeMount(){
    debugger
  },
  mounted(){
    debugger
  },
  beforeUpdate(){
    debugger
  },
  updated(){
    debugger
  },
  activated(){
    debugger
  },
  deactivated(){
    debugger
  },
  beforeDestroy(){
    debugger
  },
  destroyed(){
    debugger
  },
}
</script>

<style>
html {
  height: 100%;
}
body {
  /* background-color:#FFFFFF; */
  height: 100%;
  width: 100%;
}

img {
  object-fit: cover
}

img[lazy="loading"]{
    object-fit:scale-down!important;
 }

img[lazy="error"]{
    object-fit:scale-down!important;
 }


#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  height: 100%;
  position: relative;
	/* background-color: #F5F5F5; */
}
.xiantiao{
	width: 20%;
	height:1px;
	background:#2187e7;
	z-index: 9999;
	box-shadow:0px 0px 10px 1px rgba(0,198,255,0.7);
	-moz-animation:xiantiao1 1.5s ease-in-out infinite;
	-webkit-animation:xiantiao1 1.5s ease-in-out infinite;
	animation:xiantiao1 1.5s ease-in-out infinite;
  position: fixed;
  top: 0;
}
@-moz-keyframes xiantiao1{
	0%{
		-moz-transform: translateX(-100%);
		/* -moz-transition-duration: 4s; */
	}
	100%{ 
		-moz-transform: translateX(500%);
		/* -moz-transition-duration: 4s;	 */
	}
}
@-webkit-keyframes xiantiao1{
	0%{
		-webkit-transform: translateX(-100%);
		/* -webkit-transition-duration: 4s; */
	}
  100%{ 
		-webkit-transform: translateX(500%);
		/* -webkit-transition-duration: 4s */
	}
}
@keyframes xiantiao1{
	0%{
		transform: translateX(-100%);
		/* transition-duration: 4s; */
	}
	100%{ 
		transform: translateX(500%);
		/* transition-duration: 4s;	 */
	}
}
</style>
