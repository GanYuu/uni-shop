<template>
	<view>
		<view class="search-box">
		  <!-- 使用 uni-ui 提供的搜索组件 -->
		  <uni-search-bar @input="input" :radius="100" cancelButton="none" placeholder="今天七宝打折"></uni-search-bar>
		</view>
		<!-- 搜索历史 -->
		<view class="history-box" v-if="searchResults.length === 0">
		  <!-- 标题区域 -->
		  <view class="history-title">
		    <text>搜索历史</text>
		    <uni-icons type="trash" size="17" @click="cleanHistory"></uni-icons>
		  </view>
		  <!-- 列表区域 -->
		  <view class="history-list">
		    <uni-tag :text="item" v-for="(item, i) in historys" :key="i" @click="gotoGoodsList(item)"></uni-tag>
		  </view>
		</view>
		<view class="sugg-list" v-else>
		  <view class="sugg-item" v-for="(item, i) in searchResults" :key="i" @click="gotoDetail(item.goods_id)">
		    <view class="goods-name">{{item.goods_name}}</view>
		    <uni-icons type="arrowright" size="16"></uni-icons>
		  </view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				timer:null,
				kw:'',
				searchResults:[],
				historyList:[]
			};
		},
		computed: {
		  historys() {
		    // 注意：由于数组是引用类型，所以不要直接基于原数组调用 reverse 方法，以免修改原数组中元素的顺序
		    // 而是应该新建一个内存无关的数组，再进行 reverse 反转
		    return [...this.historyList].reverse()
		  }
		},
		methods:{
			input(val){
				this.timer&&clearTimeout(this.timer)
				this.timer = setTimeout(()=>{
					this.kw = val
					this.getSearchList()
				},500)
			},
			async getSearchList(){
				if(this.kw===''){
					this.searchResults = []
					return
				}
				const { data: res } = await uni.$http.get('/api/public/v1/goods/qsearch', { query: this.kw })
				if (res.meta.status !== 200) return uni.$showMsg()
				this.searchResults = res.message
				this.saveSearchHistory()
			},
			saveSearchHistory() {
				const set = new Set(this.historyList)
				set.delete(this.kw)
				set.add(this.kw)
				this.historyList = Array.from(set)
				uni.setStorageSync('kw',JSON.stringify(this.historyList))
			},
			gotoDetail(goods_id) {
			  uni.navigateTo({
			    // 指定详情页面的 URL 地址，并传递 goods_id 参数
			    url: '/subpkg/goods_detail/goods_detail?goods_id=' + goods_id
			  })
			},
			// 清空搜索历史记录
			cleanHistory() {
			  // 清空 data 中保存的搜索历史
			  this.historyList = []
			  // 清空本地存储中记录的搜索历史
			  uni.setStorageSync('kw', '[]')
			},
			// 点击跳转到商品列表页面
			gotoGoodsList(kw) {
			  uni.navigateTo({
			    url: '/subpkg/goods_list/goods_list?query=' + kw
			  })
			}
		},
		onLoad(){
			this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]')
		}
	}
</script>

<style lang="scss">
	.uni-searchbar{
		display:flex;
		flex-direction: row;
		position: relative;
		padding:16rpx;
		background-color: #c00000;
	}
	.search-box{
		position: sticky;
		top:0;
		z-index: 999;
	}
	.sugg-list {
	  padding: 0 5px;
	
	  .sugg-item {
	    font-size: 12px;
	    padding: 13px 0;
	    border-bottom: 1px solid #efefef;
	    display: flex;
	    align-items: center;
	    justify-content: space-between;
	
	    .goods-name {
	      // 文字不允许换行（单行文本）
	      white-space: nowrap;
	      // 溢出部分隐藏
	      overflow: hidden;
	      // 文本溢出后，使用 ... 代替
	      text-overflow: ellipsis;
	      margin-right: 3px;
	    }
	  }
	}
	.history-box {
	  padding: 0 5px;
	
	  .history-title {
	    display: flex;
	    justify-content: space-between;
	    align-items: center;
	    height: 40px;
	    font-size: 13px;
	    border-bottom: 1px solid #efefef;
	  }
	
	  .history-list {
	    display: flex;
	    flex-wrap: wrap;
	
	    .uni-tag {
	      margin-top: 5px;
	      margin-right: 5px;
	    }
	  }
	}
</style>
