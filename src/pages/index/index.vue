<template>
<view class="test2">
  <button @tap="chooseCropImage">选取裁剪图片</button>
  <simple-crop
    v-if="visible"
    :size="size"
    :src="src"
    @on-close="closeCallback"
    @on-crop-change="cropCallback"></simple-crop>
  <view>裁剪结果：</view>
  <image mode="widthFix" :src="result"></image>
  <view>原图变换：</view>
  <image mode="widthFix" :src="transformSrc"></image>
</view>
</template>

<script>
import SimpleCrop from '@/components/simple-crop/simple-crop.vue'
import { compressImage } from '@/utils/file.js'

export default {
  name: 'Demo',
  components: {
    'simple-crop': SimpleCrop
  },
  data () {
    return {
      src: null,
      visible: false,
      size: {
        width: 400,
        height: 400
      },
      result: '',
      transformSrc: ''
    }
  },
  methods: {
    // 选取裁剪图片
    chooseCropImage: function () {
      const self = this
      uni.chooseImage({
        success (res) {
          // #ifdef H5
          compressImage({
            file: res.tempFiles[0],
            type: 'image/jpeg',
            quality: 0.9,
            size: 256000,
            length: 1000
          }).then(src => {
            self.src = src
            self.visible = true
          })
          // #endif
          // #ifndef H5
          self.visible = true
          self.src = res.tempFilePaths[0]
          // #endif
        }
      })
    },

    // 裁剪图片回调
    cropCallback: function (data) {
      this.visible = false
      this.result = data.resultSrc
      this.transformSrc = data.transformSrc
    },

    // 关闭回调
    closeCallback: function (event) {
      this.visible = false
    }
  }
}
</script>

<style>
.test2 {
  text-align: center;
  padding: 10px 0;
}

.test2>button {
  width:80%;
  margin:10px auto;
}

.test2>image {
  margin:10px auto;
  width:80%;
  background-color:#eee;
  border:1rpx solid #eee;
}
</style>
