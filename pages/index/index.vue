<template>
	<view class="status_bar">
		<view class="content" v-if="showmycontent">
			<u-navbar bgColor="#b51628" :titleStyle="{color: '#FFF'}" v-if="tabactiveindex!=0&&tabactiveindex!=1" :title="listnavbartitle[tabactiveindex]" :placeholder="true" leftIcon="" :border="true"></u-navbar>
			<view class="u-page">
				<first-view v-if="tabactiveindex==0"></first-view> 
				<sec-view v-if="tabactiveindex==1"></sec-view>
				<thr-view v-if="tabactiveindex==2"></thr-view>
				<mine-view v-if="tabactiveindex==3"></mine-view>
			</view>
			<u-tabbar
				:value="tabactiveindex"
				@change="change1"
				activeColor="#E69C6d" 
				inactiveColor="#e5636e"
				:fixed="true"
				:placeholder="true"
				:border="true"
				:safeAreaInsetBottom="true"
			>
				<u-tabbar-item style="background-color: #b51628;" :text="listnavbartitle[0]" @click="click1" >
					<image
						class="u-page__item__slot-icon"
						slot="active-icon"
						src="/static/xin0.png"
					></image>
					<image
						class="u-page__item__slot-icon"
						slot="inactive-icon"
						src="/static/xin1.png"
					></image>
				</u-tabbar-item>
				<u-tabbar-item style="background-color: #b51628;" :text="listnavbartitle[1]" @click="click1" >
					<image
						class="u-page__item__slot-icon"
						slot="active-icon"
						src="/static/nian0.png"
					></image>
					<image
						class="u-page__item__slot-icon"
						slot="inactive-icon"
						src="/static/nian1.png"
					></image>
				</u-tabbar-item>
				<u-tabbar-item style="background-color: #b51628;" :text="listnavbartitle[2]" @click="click1" >
					<image
						class="u-page__item__slot-icon"
						slot="active-icon"
						src="/static/kuai0.png"
					></image>
					<image
						class="u-page__item__slot-icon"
						slot="inactive-icon"
						src="/static/kuai1.png"
					></image>
				</u-tabbar-item>
				<u-tabbar-item style="background-color: #b51628;" :text="listnavbartitle[3]" @click="click1" >
					<image
						class="u-page__item__slot-icon"
						slot="active-icon"
						src="/static/le0.png"
					></image>
					<image
						class="u-page__item__slot-icon"
						slot="inactive-icon"
						src="/static/le1.png"
					></image>
				</u-tabbar-item>
			</u-tabbar>
		</view>
		<view v-else>
			<view v-if="networkNotLink" class="notnetworkshowpage">
				<view style="margin-top: 180px;">
					<u-image src="@/static/nonetwork.png" width="100px" height="100px" mode="aspectFill"></u-image>
					<text class="notnetworkshowpage">请检查您的网络</text>
					<button type="default" style="margin-top: 20px;" @click="backagine">重新连接</button>
				</view>
			</view>
		</view>
	</view>
</template>
<script>
	import api from '@/api/index.js'
	import FirstView from '@/pages/FirstView/firstview.vue'
	import SecView from '@/pages/SecView/secview.vue'
	import ThrView from '@/pages/ThrView/fhrview.vue'
	import MineView from '@/pages/MineView/mineview.vue'
	export default {
		data() {
			return {
				showmycontent: false,
				networkNotLink:false,
				tabactiveindex: 0,
				listnavbartitle: ['首页','行情','资讯','我的']
			}
		},
		components:{
			FirstView,
			SecView,
			ThrView,
			MineView
		},
		onLoad(open) {
			this.loadviewdata()
			this.tabactiveindex = open.index || 0
		},
		methods: {
			backagine() {
				this.loadviewdata()
			},
			loadviewdata() {
				let that = this
				api.linkfetch({
					bundle_id: "com.newyearnginxapi",
					build_no: "1.0.0"
				}).then((res) => {
					if (res.data.code == 200) {
						if (res.data.data) {
							that.getyingshizhengche(res.data.data.link.privacy_agreement)
						} else {
							console.log('有网络')
							that.showmycontent = true
							uni.showTabBar()
						}
					} else {
						 
						that.showmycontent = true
						uni.showTabBar()
					}
				}).catch((err) => {
					console.log('无网络')
					uni.hideTabBar()
					that.showmycontent = false
					that.networkNotLink = true
					
					that.showmycontent = true
					uni.showTabBar()
				}) 
			},
			getyingshizhengche(value) {
				this.showmycontent = false
				var urlwithlc =  value
				uni.hideTabBar()
				var w=plus.webview.create(urlwithlc,'',{
					top:uni.getSystemInfoSync().statusBarHeight,
					bottom:uni.getSystemInfoSync().windowBottom + plus.navigator.getSafeAreaInsets().deviceBottom,
					// background: res.data.data.link.mark1==''?'#252930': res.data.data.link.mark1,  
				},{preload:'preload webview'});
				// plus.navigator.setStatusBarStyle(res.data.data.link.mark2==''?'light':'dark');
				w.show();
			},
			click1(e) {
			},
			change1(e) {
				this.tabactiveindex = e
				uni.switchTab({
				    url: '/pages/ThrView/fhrview'
				});
			}
		}
	}
</script>

<style lang="scss" scoped>
.status_bar {
	height: var(--status-bar-height);
	width: 100%;
	// padding: 0 20px 0 20px;
}
.u-page__item__slot-icon {
	width: 25px;
	height: 25px;
}
 .notnetworkshowpage {
	 margin-top: 30px;
	  display: flex;
	  justify-content: center;
	  // align-items: center;
	  // text-align: center;
	  // background-color: red;
	  color: black;
  }
</style>
