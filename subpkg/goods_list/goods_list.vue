<template>
	<view>
		<view class="goods-list">
				<view v-for="(goods,i) in goodsList" :key="i" @click="gotoDetail(goods)">
                      <my-goods :goods="goods"></my-goods>
				</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				queryObj:{
					query:"",
					cid:"",
					pagenum:"",
					pagesize:10
				},
				goodsList:[],
				total:0,
				defaultPic:"",
				isloading:false
			}
		},

		onLoad(options) {
			console.log(options)
			this.queryObj.query=options.query||''
			this.queryObj.cid=options.cid||''
			this.getGoodsList()
			
		},
		methods:{
			async getGoodsList(cb){
			this.isloading=true
			const {data:res}=await uni.$http.get('/api/public/v1/goods/search',this.queryObj)
			this.isloading=false
			cb&&cb()
			if(res.meta.status!==200) return uni.$showMsg()
			this.goodsList=[...this.goodsList,...res.message.goods]
			this.total=res.message.total
			},
			gotoDetail(goods){
				uni.navigateTo({
					url:"/subpkg/goods_detail/goods_detail?goods_id="+goods.goods_id
				})
			}
		},
		onReachBottom() {
			if(this.queryObj.pagenum*this.queryObj.pagesize>=this.total) return uni.$showMsg("数据加载完毕")
			if(this.isloading)return 
			this.queryObj.pagenum++
			this.getGoodsList()
		},
		onPullDownRefresh() {
			this.queryObj.pagenum=1
			this.total=0
			this.isloading=false
			this.goodsList=[]
			this.getGoodsList(()=>uni.stopPullDownRefresh())
		}
	}
</script>

<style lang="scss">
  .goods-item{
	  display: flex;
	  padding: 10px 5px;
	  border-bottom: 1px solid #f0f0f0;
	  .goods-item-left{
		  margin-right: 5px;
		 .goods-pic{
			 width: 100px; 
			 height: 100px;
			 display: block;
		 } 
	  }
	  .goods-item-right{
		  display: flex;
		  flex-direction: column;
		  justify-content: space-between;
		  .goods-name{
			  font-size: 13px;
		  }
		  .goods-info-box{
			  .goods-price{
				  color: #C00000;
				  font-size: 16px;
			  }
		  }
	  }
  }
</style>
