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
import SuperGif from '../../static/libgif';
import GIF from '../../static/gif';
import { getGifWorker } from '../../static/gif.worker';
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
      img_list: [], // 保存每一帧gif
      img_list_index: 0, // 每一帧裁剪索引
      eachGif: [], // 暂时保存处理好的每一帧图片
    };
  },
  computed: {
  },
  components: {

  },
  created() {
    //
    // this.showItemImg = this.imageUrl[this.index].url;
  },
  activated() {
    //
  },
  mounted() {
    this.croppering();
  },
  watch: {
  },
  methods: {
    croppering() {
      if (this.imageUrl[this.index].isGif) {
        this.gifInit();
      } else {
        this.init();
      }
    },
    init() {
      this.showItemImg = this.imageUrl[this.index].url;
      console.log(this.$refs.image);
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
        autoCropArea: 0.8, // 裁剪框为图片大小的80%
        zoomOnWheel: false,
        movable: false,
        rotatable: false,
        scalable: false,
        zoomOnTouch: false,
      });
    },
    sureSava() {
      /**
       * 保存时，拿到当前cropper实例中数据，并按高压缩比例输出，输出为jpeg图片
       * @type {string}
       */
      this.afterImg = this.myCropper.getCroppedCanvas({
        imageSmoothingQuality: 'high',
      }).toDataURL('image/jpeg');
      this.index++;
      /**
       * 向父组件传递裁剪过后的值
       * 判断是否还有数据，有的话再次调用croper进行实例化操作
       */
      if (this.imageUrl.length > this.index) {
        // 通知父组件保存值
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
    gifInit() {
      this.pre_load_gif(this.imageUrl[this.index].url);
    },
    dataURLtoFile(dataurl, filename) {
      const arr = dataurl.split(',');
      const mime = arr[0].match(/:(.*?);/)[1];
      const bstr = atob(arr[1]);
      let n = bstr.length;
      const u8arr = new Uint8Array(n);
      while (n--) {
        u8arr[n] = bstr.charCodeAt(n);
      }
      return new File([u8arr], filename, { type: mime });
    },
    // 将canvas转换成file对象
    convertCanvasToImage(canvas, filename) {
      return this.dataURLtoFile(canvas.toDataURL('image/png'), filename);
    },
    base64ToBlob(base64) {
      const parts = base64.split(';base64,');
      const contentType = parts[0].split(':')[1];
      const raw = window.atob(parts[1]);
      const rawLength = raw.length;

      const uInt8Array = new Uint8Array(rawLength);

      for (let i = 0; i < rawLength; i += 1) {
        uInt8Array[i] = raw.charCodeAt(i);
      }
      return new Blob([uInt8Array], { type: contentType });
    },
    pre_load_gif(gif_source) {
      // var img_list = [];
      const gifImg = document.createElement('img');
      const gif = this.base64ToBlob(gif_source);
      // gif库需要img标签配置下面两个属性
      gifImg.setAttribute('rel:animated_src', URL.createObjectURL(gif));
      gifImg.setAttribute('rel:auto_play', '1');
      document.body.appendChild(gifImg);
      // console.log(gifImg);
      // 新建gif实例
      const rub = new SuperGif({ gif: gifImg });
      rub.load(() => {
        const img_list = [];
        for (let i = 1; i <= rub.get_length(); i++) {
          // 遍历gif实例的每一帧
          rub.move_to(i);
          // 将每一帧的canvas转换成file对象
          const cur_file = this.convertCanvasToImage(rub.get_canvas(), `'test'-${i}`);
          img_list.push({
            file_name: cur_file.name,
            url: URL.createObjectURL(cur_file),
            file: cur_file,
          });
        }
        this.img_list = img_list;
        this.gifCropper();
      });
      gifImg.remove();
    },
    gifCropper() {
      this.showItemImg = this.img_list[this.img_list_index].url;
      this.$nextTick(() => {
        this.myCropper = new Cropper(this.$refs.image, {
          viewMode: 1,
          dragMode: 'none',
          checkOrientation: false,
          checkCrossOrigin: false,
          guides: false,
          center: false,
          background: false,
          autoCropArea: 1,
          zoomOnWheel: false,
          movable: false,
          rotatable: false,
          scalable: false,
          zoomOnTouch: false, // 允许缩放图片
          ready: (e) => {
            // 开始剪裁
            this.sureSavaGif(e.srcElement.width);
          },

        });
      });
    },
    sureSavaGif(imgWidth) {
      if (imgWidth > 800) {
        // eslint-disable-next-line no-param-reassign
        imgWidth = 800;
      }
      const afterImg = this.myCropper.getCroppedCanvas({
        imageSmoothingQuality: 'height',
        width: imgWidth,
      }).toDataURL('image/jpeg');
      this.eachGif.push(afterImg);
      // 销毁实例
      this.myCropper.destroy();
      // 判断gif是否每一帧都裁剪完毕
      this.img_list_index++;
      if (this.img_list.length > this.img_list_index) {
        this.$nextTick(() => {
          this.gifCropper();
        });
      } else {
        // this.img_list_index = 0;
        // this.eachGif = [];
        this.mergeGif();
      }
    },
    // gif合并
    async mergeGif() {
      // const width=300;
      // const height=300;
      const gif = new GIF({
        workers: 2,
        quality: 10,
        // width,
        // height,
        workerScript: getGifWorker(), // 自定义worker地址
      });
      let j = 0;
      const canvas = document.createElement('canvas');
      const ctx = canvas.getContext('2d');
      for (let i = 1; i <= this.eachGif.length; i++) {
        // eslint-disable-next-line no-await-in-loop
        const imgImage = await this.loading(i);
        canvas.width = imgImage.width;
        canvas.height = imgImage.height;
        ctx.fillStyle = '#fff';
        ctx.fillRect(0, 0, canvas.width, canvas.height);
        ctx.drawImage(imgImage, 0, 0, canvas.width, canvas.height);
        gif.addFrame(canvas, { copy: true, delay: 50 });
        j++;
        if (j >= this.eachGif.length) {
          gif.render();
        }
      }
      gif.on('finished', async (blob) => {
        const result = await this.blobToBase64(blob);
        console.log(result);
        console.log(this.index);
        this.$emit('imgCropped', { src: result, id: this.imageUrl[this.index].id });
        // 生成图片链接
        // const url = URL.createObjectURL(blob);
        // console.log(url);
        // const a = document.createElement('a');
        // a.href = URL.createObjectURL(blob);
        // a.download = 'test.gif';
        // a.click();
        gif.abort();
        const gifsNode = document.getElementsByClassName('jsgif');
        console.log(gifsNode[0]);
        gifsNode[0].remove();
        // 是否还要裁剪
        this.index++;
        console.log(this.imageUrl);
        if (this.imageUrl.length > this.index) {
          // eslint-disable-next-line no-unused-expressions
          this.eachGif = [];
          this.img_list_index = 0;
          this.croppering();
        } else {
          this.index = 0;
          this.img_list_index = 0;
          // this.$refs.input.value = '';
          this.$emit('closeCropper');
        }
      });
    },
    loading(i) {
      return new Promise((resolve) => {
        const imgImage = new Image();
        imgImage.src = this.eachGif[i - 1];
        document.body.appendChild(imgImage);
        // console.log(imgImage);
        imgImage.onload = () => {
          resolve(imgImage);
          imgImage.parentNode.removeChild(imgImage);
        };
      });
    },
    blobToBase64(blob) {
      return new Promise((resolve, reject) => {
        const fileReader = new FileReader();
        fileReader.onload = (e) => {
          resolve(e.target.result);
        };
        // readAsDataURL
        fileReader.readAsDataURL(blob);
        fileReader.onerror = () => {
          reject(new Error('blobToBase64 error'));
        };
      });
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
