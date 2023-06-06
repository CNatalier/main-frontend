<template>
  <view class="content">
    <view class="divider"></view>
    <uni-nav-bar title="AI 识物"></uni-nav-bar>
    <button @click="chooseImage">选择图片</button>
    <view class="image-view">
      <img :src="imagePath" v-if="imagePath" />
    </view>
    <view class="result" v-if="result">
      <text>{{result}}</text>
    </view>
    <view class="divider"></view>
        <uni-nav-bar title="AI 翻译"></uni-nav-bar>
        <view class="form">
          <textarea class="textarea" v-model="textToTranslate" placeholder="输入要翻译的中文文字"></textarea>
          <button class="submit-button" @click="submit">翻译</button>
        </view>
        <view class="result" v-if="translatedText">
          <text>{{translatedText}}</text>
        </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        imagePath: '',
        result: '',
        textToTranslate: '',
        translatedText: ''
      }
    },
    methods: {
      chooseImage() {
        uni.chooseImage({
          sourceType: ['album', 'camera'],
          success: chooseImageRes => {
            const tempFilePaths = chooseImageRes.tempFilePaths
            this.imagePath = tempFilePaths[0]
            //this.imagePath = tempFilePaths[0]
            this.uploadImage(tempFilePaths[0])
          },
          fail: err => {
            // handle error
            console.log(err)
            uni.showToast({
              title: 'Failed to access image',
              icon: 'none',
              duration: 2000
            })
          }
        })
      },

      onLoad() {
        this.resetData()
      },
      onShow() {
              this.resetData()
          },
      submit() {
              uni.request({
                url: 'http://123.60.11.207:80/translate',
                data: { text: this.textToTranslate },
                method: 'POST',
                success: res => {
                  if (res.data.code === 200) {
                    this.translatedText = res.data.data
                  } else {
                    uni.showToast({
                      title: res.data.message,
                      icon: 'none',
                      duration: 2000
                    })
                  }
                }
              })
            },
            resetData() {
                    this.imagePath = ''
                    this.result = ''
                    this.textToTranslate = ''
                    this.translatedText = ''
                  },
      uploadImage(path) {
        uni.uploadFile({
          url: 'http://123.60.11.207:80/predict', 
          filePath: path,
          name: 'img',
          success: uploadFileRes => {
            const data = JSON.parse(uploadFileRes.data)
            if (data.code === 200) {
              this.imagePath='http://123.60.11.207:80/static/result.jpg'+ '?t='+(+new Date())
              this.$forceUpdate()
            } else {
              uni.showToast({
                title: data.message,
                icon: 'none',
                duration: 2000
              })
            }
          }
        })
      }
    }
  }
</script>

<style>
  .divider {
    height: 1px;
    background-color: #000;  
    width: 100%;
  }
  .image-view img {
    width: 100%;
  }
    .form {
      margin: 20px;
    }
    .textarea {
      width: 100%;
      height: 100px;
    }
    .submit-button {
      margin-top: 20px;
    }
    .result {
      margin-top: 20px;
      text-align: center;
      font-size: 20px;
    }
  .result {
    margin-top: 20px;
    text-align: center;
    font-size: 20px;
  }
</style>
