<template>
  <div class="cropper-wrap">

    <div class="top">
      <div
        class="cancel"
        @click="goBack"
        @keyup="goBack"
      >cancel</div>
      <div class="title">cut</div>
      <div
        class="btn"
        @click="sureSava"
        @keyup="sureSava"
      >next</div>
    </div>

    <div class="container">
      <div class="img-container" v-if="cropping">
        <img :src="showItemImg" ref="image" alt="">
      </div>
      <div class="afterCropper" v-else>
        <img :src="afterImg" alt="">
      </div>
    </div>

  </div>
</template>

<script>
import Cropper from 'cropperjs';
import 'cropperjs/dist/cropper.css';

export default {
  name: 'cropper',
  props: {
    imageUrl: {
      type: Array,
    },
  },
  data() {
    return {
      myCropper: null, // 用来保存Cropper实例
      afterImg: '', // 裁剪后图片
      cropping: true, // 正在剪裁,剪裁完后为false
      showItemImg: '', // 用来展示裁剪图片
      index: 0, // 裁剪索引，用来
    };
  },
  computed: {
  },
  components: {

  },
  created() {
    //
    this.showItemImg = this.imageUrl[this.index].url;
  },
  activated() {
    //
  },
  mounted() {
    this.init();
  },
  watch: {
  },
  methods: {
    init() {
      this.myCropper = new Cropper(this.$refs.image, {
        viewMode: 2,
        dragMode: 'crop',
        initialAspectRatio: 1,
        // aspectRatio: 1,
        checkOrientation: false,
        checkCrossOrigin: false,
        guides: false,
        center: false,
        background: false,
        autoCropArea: 0.8,
        zoomOnWheel: false,
        movable: false,
        rotatable: false,
        scalable: false,
        zoomOnTouch: false,
      });
    },
    sureSava() {
      this.afterImg = this.myCropper.getCroppedCanvas({
        imageSmoothingQuality: 'high',
      }).toDataURL('image/jpeg');
      this.index++;
      if (this.imageUrl.length > this.index) {
        this.$emit('imgCropped', { src: this.afterImg, id: this.imageUrl[this.index - 1].id });
        this.nextImg();
      } else {
        this.$emit('imgCropped', { src: this.afterImg, id: this.imageUrl[this.index - 1].id });
        this.$emit('closeCropper');
      }
    },
    // 下一张图片裁剪
    nextImg() {
      this.myCropper.destroy();
      this.showItemImg = this.imageUrl[this.index].url;
      this.init();
    },
    goBack() {
      // 关闭剪裁器
      this.$emit('closeCropper');
    },
  },
};

</script>

<style lang="scss" scoped>
.cropper-wrap {
  position: fixed;
  top: 0;
  bottom: 0;
  width: 750px;
  background: #000;
.top {
  position: relative;
  height: 104.1px;
  padding: 0 20.8px 0;
  font-weight: 500;
  font-size: 25px;
  border-bottom: 1px solid rgba(0, 0, 0, .1);
  background: #fff;
  z-index: 15;
  display: flex;
  justify-content: space-between;
  align-items: center;
.cancel {
  width: 142.3px;
  color: rgba(0, 0, 0, .6);
}
.title {
  width: fit-content;
  color: #000;
  font-size: 34.7px;
}
.btn {
  width: 142.3px;
  height: 50px;
  background: #E32A38;
  border-radius: 4.1px;
  color: #fff;
  display: flex;
  justify-content: center;
  align-items: center;
.loading {
  background-color: pink;
}
}
}

.container{
  position: absolute;
  top: 104.1px;
  left: 0;
  bottom: 0;
  right: 0;
  z-index: 13;
}
.img-container{
  height: 100%;
}
.afterCropper{
  height: 100%;
}
.afterCropper img{
  width: 100%;
  height: 100%;
}
}
</style>
