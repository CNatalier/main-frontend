<template>
	<view class="content">
    <view class="divider"></view>
    <uni-nav-bar title="公交线路查询"></uni-nav-bar>
		<view class="search-box">
			<!-- mSearch组件 如果使用原样式，删除组件元素-->
			<mSearch class="mSearch-input-box" :mode="2" button="inside" :placeholder="defaultKeyword" @search="doSearch(false)"  @confirm="doSearch(false)" v-model="keyword"></mSearch>

		</view>
		<view class="search-keyword" >
			<scroll-view class="keyword-list-box" v-show="isShowKeywordList" scroll-y>
				<block v-for="(row,index) in keywordList" :key="index">
					<view class="keyword-entry" hover-class="keyword-entry-tap" >
						<view class="keyword-text" @tap.stop="doSearch(keywordList[index].keyword)">
							<rich-text :nodes="row.htmlStr"></rich-text>
						</view>
						
					</view>
				</block>
				
			</scroll-view>
			
		</view>
    <scroll-view class="bus-data-list" scroll-y style="height: 50vh;">
      
      <view class="bus-data-item" v-for="(item, index) in busData" :key="index">
        <text style="color: red;">起点站: {{ item.start_station }}</text>
        <text style="color: green;">终点站: {{ item.end_station }}</text>
        <text style="color: blue;">价格: {{ item.price }}</text>
      </view>
    </scroll-view>
    <view class="divider"></view>
    <uni-nav-bar title="全国公交数据分析展示"></uni-nav-bar>
    <view class="button-container">
         <button @click="getAndShowImage(1)">柱 图</button>
         <button @click="getAndShowImage(2)">饼 图</button>
         <button @click="getAndShowImage(3)">地 图</button>
       </view>
       <view class="image-view" v-if="currentImage">
         <img :src="currentImage" />
       </view>
 

    			 

	</view>
</template>

<script>
  import uniPopup from '@/uni_modules/uni-popup/components/uni-popup/uni-popup.vue'
	//引用mSearch组件，如不需要删除即可
	import mSearch from '@/components/mehaotian-search-revision/mehaotian-search-revision.vue'
	export default {
		data() {
			return {
        imageBase64: '',
        busData: '',
				defaultKeyword: '',
				keyword: '',
        showImagePopup: false,
        currentImage: null,
				oldKeywordList: [],
				hotKeywordList: [],
				keywordList: [], 
				forbid: '',
        img1: null,
        img2: null,
        img3: null,
				isShowKeywordList: false,
        imageUrls: [
                'https://pic1.imgdb.cn/item/64734358f024cca173bf1d91.jpg',
                'https://pic1.imgdb.cn/item/6473437cf024cca173bf444b.jpg',
                'https://pic1.imgdb.cn/item/64734388f024cca173bf4e36.png'
              ]
			}
		},
		onLoad() {
			this.init()
      this.clearData()
      this.resetImage()
		},
    onShow() {
            this.blur()
            this.clearData()
            this.resetImage()
        },
		components: {
			//引用mSearch组件
			mSearch,
		},
		methods: {
			init() {
				this.loadDefaultKeyword()
        this.blur()
			},
       getAndShowImage(imgNumber) {
            this.currentImage = this.imageUrls[imgNumber - 1]
          },
      // getAndShowImage(imgNumber) {
      //         uni.request({
      //           url: 'http://123.60.11.207:80/buses_analysis',
      //           method: 'GET',
      //           success: res => {
      //             if (res.data.code === 200) {
      //               this.imageBase64 = 'data:image/png;base64,' + res.data.data[`img${imgNumber}`]
      //             } else {
      //               uni.showToast({
      //                 title: res.data.message,
      //                 icon: 'none',
      //                 duration: 2000
      //               })
      //             }
      //           }
      //         })
      //       }, 
      resetImage() {
            this.currentImage = ''
      },
			blur(){
				uni.hideKeyboard()
			},
      clearData() {
        this.keyword = '' 
        this.busData = '' 
                  
              },
			//加载默认搜索关键字
			loadDefaultKeyword() {
				//定义默认搜索关键字，可以自己实现ajax请求数据再赋值,用户未输入时，以水印方式显示在输入框，直接不输入内容搜索会搜索默认关键字
				this.defaultKeyword = '请输入城市名'
			},
      getAndShowImage1() {
        console.log('hhhhhhhhhhhhhhhhhhhhhhhhhhh')
        uni.request({
          url: 'http://123.60.11.207:80/buses_analysis',
          method: 'GET',
          success: res => {
            if (res.statusCode === 200) {
              const data = res.data
              switch (data.code) {
                case 200:

                this.currentImage = 'data:image/jpg ;base64,' + data.data['img1']
                this.showImagePopup = true
                console.log(this.currentImage)
                  // Query successful
                  uni.showToast({
                    title: '查询kkkkkk成功',
                    icon: 'success',
                    duration: 2000
                  })
                  break
                default:
                  uni.showToast({
                    title: 'Unknown Error',
                    icon: 'none',
                    duration: 2000
                  })
                  break
              }
            } else {
              uni.showToast({
                title: 'Request Failed',
                icon: 'none',
                duration: 2000
              })
            }
          },
          fail: err => {
            uni.showToast({
              title: 'Network Request Failed',
              icon: 'none',
              duration: 2000
            })
          }
        })
			},
    
			//高亮关键字
			drawCorrelativeKeyword(keywords, keyword) {
				var len = keywords.length,
					keywordArr = []
				for (var i = 0; i < len; i++) {
					var row = keywords[i]
					//定义高亮#9f9f9f
					var html = row[0].replace(keyword, '<span style=\'color: #9f9f9f;\'>' + keyword + '</span>')
					html = '<div>' + html + '</div>'
					var tmpObj = {
						keyword: row[0],
						htmlStr: html
					}
					keywordArr.push(tmpObj)
				}
				return keywordArr
			},
			//执行搜索 
			doSearch(keyword) {
			  keyword = keyword===false ? this.keyword : keyword
			  this.keyword = keyword
			  this.saveKeyword(keyword) //保存为历史 
			
			  // Request bus info
			  uni.request({
			    url: 'http://123.60.11.207:80/businfo',
			    method: 'GET',
			    data: { city: this.keyword },
			    success: res => {
			      if (res.statusCode === 200) {
			        const data = res.data
              this.busData = data.data
              console.log(data.data)
			        switch (data.code) {
			          case 200:
			            // Query successful
			            uni.showToast({
			              title: '查询成功',
			              icon: 'success',
			              duration: 2000
			            })
			            break
			          case -1:
			            uni.showToast({
			              title: data.message,
			              icon: 'none',
			              duration: 2000
			            })
			            break
			          default:
			            uni.showToast({
			              title: 'Unknown Error',
			              icon: 'none',
			              duration: 2000
			            })
			            break
			        }
			      } else {
			        uni.showToast({
			          title: 'Request Failed',
			          icon: 'none',
			          duration: 2000
			        })
			      }
			    },
			    fail: err => {
			      uni.showToast({
			        title: 'Network Request Failed',
			        icon: 'none',
			        duration: 2000
			      })
			    }
			  })
			},

			//保存关键字到历史记录
			saveKeyword(keyword) {
				uni.getStorage({
					key: 'OldKeys',
					success: res => {
						var OldKeys = JSON.parse(res.data)
						var findIndex = OldKeys.indexOf(keyword)
						if (findIndex == -1) {
							OldKeys.unshift(keyword)
						} else {
							OldKeys.splice(findIndex, 1)
							OldKeys.unshift(keyword)
						}
						//最多10个纪录
						OldKeys.length > 10 && OldKeys.pop()
						uni.setStorage({
							key: 'OldKeys',
							data: JSON.stringify(OldKeys)
						})
						this.oldKeywordList = OldKeys //更新历史搜索
					},
					fail: e => {
						var OldKeys = [keyword]
						uni.setStorage({
							key: 'OldKeys',
							data: JSON.stringify(OldKeys)
						})
						this.oldKeywordList = OldKeys //更新历史搜索
					}
				})
			}
		}
	}
</script>
<style>
  .button-container {
    display: flex;
    justify-content: space-between;
  }
  
  .button-container button {
    flex: 1;
    margin: 10px;
  }

  .bus-data-list {
      width: 100%;
    }
  
    .bus-data-item {
      display: flex;
      justify-content: space-between;
      border-bottom: 1px solid #000;
      padding: 10px 0;
    }
  .button-container {
      display: flex;
      justify-content: space-around;
      margin-bottom: 20px;
    }
    .image-view img {
      width: 100%;
    }
    .bus-data-item text {
      margin: 5px 10px;
    }
    .divider {
        height: 1px;
        background-color: #000;  /* Change color as per your requirement */
        width: 100%;
    }
	view{display:block;}
	.search-box {width:95%;background-color:rgb(242,242,242);padding:15upx 2.5%;display:flex;justify-content:space-between;position:sticky;top: 0;}
	.search-box .mSearch-input-box{width: 100%;}
	.search-box .input-box {width:85%;flex-shrink:1;display:flex;justify-content:center;align-items:center;}
	.search-box .search-btn {width:15%;margin:0 0 0 2%;display:flex;justify-content:center;align-items:center;flex-shrink:0;font-size:28upx;color:#fff;background:linear-gradient(to right,#ff9801,#ff570a);border-radius:60upx;}
	.search-box .input-box>input {width:100%;height:60upx;font-size:32upx;border:0;border-radius:60upx;-webkit-appearance:none;-moz-appearance:none;appearance:none;padding:0 3%;margin:0;background-color:#ffffff;}
	.placeholder-class {color:#9e9e9e;}
	.search-keyword {width:100%;background-color:rgb(242,242,242);}
	.keyword-list-box {height:calc(100vh - 110upx);padding-top:10upx;border-radius:20upx 20upx 0 0;background-color:#fff;}
	.keyword-entry-tap {background-color:#eee;}
	.keyword-entry {width:94%;height:80upx;margin:0 3%;font-size:30upx;color:#333;display:flex;justify-content:space-between;align-items:center;border-bottom:solid 1upx #e7e7e7;}
	.keyword-entry image {width:60upx;height:60upx;}
	.keyword-entry .keyword-text,.keyword-entry .keyword-img {height:80upx;display:flex;align-items:center;}
	.keyword-entry .keyword-text {width:90%;}
	.keyword-entry .keyword-img {width:10%;justify-content:center;}
	.keyword-box {height:calc(100vh - 110upx);border-radius:20upx 20upx 0 0;background-color:#fff;}
	.keyword-box .keyword-block {padding:10upx 0;}
	.keyword-box .keyword-block .keyword-list-header {width:94%;padding:10upx 3%;font-size:27upx;color:#333;display:flex;justify-content:space-between;}
	.keyword-box .keyword-block .keyword-list-header image {width:40upx;height:40upx;}
	.keyword-box .keyword-block .keyword {width:94%;padding:3px 3%;display:flex;flex-flow:wrap;justify-content:flex-start;}
	.keyword-box .keyword-block .hide-hot-tis {display:flex;justify-content:center;font-size:28upx;color:#6b6b6b;}
	.keyword-box .keyword-block .keyword>view {display:flex;justify-content:center;align-items:center;border-radius:60upx;padding:0 20upx;margin:10upx 20upx 10upx 0;height:60upx;font-size:28upx;background-color:rgb(242,242,242);color:#6b6b6b;}
</style>
