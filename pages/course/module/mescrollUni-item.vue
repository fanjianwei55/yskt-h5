<template>
    <!-- 
    swiper中的transfrom会使fixed失效,此时用height固定高度; 
    swiper中无法触发mescroll-mixins.js的onPageScroll和onReachBottom方法,只能用mescroll-uni,不能用mescroll-body
    -->
    <!-- ref动态生成: 字节跳动小程序编辑器不支持一个页面存在相同的ref (如不考虑字节跳动小程序可固定值为 ref="mescrollRef") -->
    <!-- top的高度等于悬浮菜单tabs的高度 -->
     <mescroll-uni :ref="'mescrollRef'+i" @init="mescrollInit" :height="height" :down="downOption" @down="downCallback" :up="upOption" @up="upCallback" @emptyclick="emptyClick">
        <!-- 数据列表 -->
        <goods-list :list="list"></goods-list>
    </mescroll-uni>
</template>

<script>
	import MescrollMixin from "@/uni_modules/mescroll-uni/components/mescroll-uni/mescroll-mixins.js";
	import MescrollMoreItemMixin from "@/uni_modules/mescroll-uni/components/mescroll-uni/mixins/mescroll-more-item.js";
    import goodsList from "./goods-list.vue";
    // import goodsList from '@/components/module/good.vue';
	export default {
		mixins: [MescrollMixin,MescrollMoreItemMixin], // 注意此处还需使用MescrollMoreItemMixin (必须写在MescrollMixin后面)
        components:{
            goodsList
        },
		data() {
			return {
				downOption:{
					auto:false // 不自动加载 (mixin已处理第一个tab触发downCallback)
				},
				upOption:{
					auto:false, // 不自动加载
					// page: {
					// 	num: 0, // 当前页码,默认0,回调之前会加1,即callback(page)会从1开始
					// 	size: 10 // 每页数据的数量
					// },
					noMoreSize: 4, //如果列表已无数据,可设置列表的总数量要大于半页才显示无更多数据;避免列表数据过少(比如只有一条数据),显示无更多数据会不好看; 默认5
					empty:{
                        icon: '/static/mescroll-empty.png',
						tip: '~ 暂无数据 ~', // 提示
						// btnText: '去看看'
					},
                    textNoMore:'没有更多了'
				},
                list:[],
			}
		},
		props:{
			i: Number, // 每个tab页的专属下标 (除了支付宝小程序必须在这里定义, 其他平台都可不用写, 因为已在MescrollMoreItemMixin定义)
			index: { // 当前tab的下标 (除了支付宝小程序必须在这里定义, 其他平台都可不用写, 因为已在MescrollMoreItemMixin定义)
				type: Number,
				default(){
					return 0
				}
			},
			tabs: { // 为了请求数据,演示用,可根据自己的项目判断是否要传
				type: Array,
				default(){
					return []
				}
			},
			height: [Number,String] ,// mescroll的高度
            keyword: {
                type: String,
                default(){
                	return ''
                }
            }
		},
		methods: {
			/*下拉刷新的回调 */
			downCallback() {
				// 这里加载你想下拉刷新的数据, 比如刷新轮播数据
				// loadSwiper();
				// 下拉刷新的回调,默认重置上拉加载列表为第一页 (自动执行 page.num=1, 再触发upCallback方法 )
				this.mescroll.resetUpScroll()
			},
			/*上拉加载的回调: 其中page.num:当前页 从1开始, page.size:每页数据条数,默认10 */
			upCallback(page) {
				//联网加载数据
				console.log("loadding.......")
				let name = this.tabs[this.i].name
				let hasFree;
				let hasLive;
				console.log((this.index))
				if(this.index === 0) {
					hasFree = ""
					hasLive = ""
				} else {
					if(this.index == 1) {
						hasFree = 0
						hasLive = ""
					}
					if(this.index == 2) {
						hasFree = 1
						hasLive = ""
					}
					if(this.index == 3) {
						hasFree = ""
						hasLive = 1
					}
					
				}
                let httpData = {
					hasFree,
					hasLive,
                    pageNo: page.num,
                    pageSize: 10,
                }
                uni.$u.http.post('/user/info/public/courseList', httpData, {custom: {load:false}}).then((res) => {
                    //联网成功的回调,隐藏下拉刷新和上拉加载的状态;
                    this.mescroll.endSuccess(res.list.length);
                    //设置列表数据
                    if(page.num == 1) this.list = []; //如果是第一页需手动制空列表
                    this.list=this.list.concat(res.list); //追加新数据
                }).catch((err) =>{
                    //联网失败, 结束加载
                    this.mescroll.endErr();
                })
			},
			//点击空布局按钮的回调
			emptyClick(){
				uni.showToast({
					title:'点击了按钮,具体逻辑自行实现'
				})
			},
            // 搜索
            doSearch(){
            	this.list = []; // 先清空列表,显示加载进度
            	this.mescroll.resetUpScroll();
            },
		}
	}
</script>