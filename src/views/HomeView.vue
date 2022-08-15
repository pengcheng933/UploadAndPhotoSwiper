<template>
  <div class="home">
    <div class="top">
      <div @click="close" @keyup="close">关闭</div>
      <div @click="upload" @keyup="upload">上传</div>
    </div>
<!--    输入框-->
    <div class="inputBox">
      <label for="story">
        <textarea
          id="story" name="story"
          v-model="textarea"
        >
        </textarea>
      </label>
    </div>
<!--    展示图片-->
    <div class="image">
      <img
        v-for="(item,index) in perImg"
        :key="index"
        :src="item.src"
        alt=""
        @click="imgClick(index)"
        @keyup="imgClick(index)"
        ref="img"
      />
    </div>
<!--    上传，图标，添加标签-->
    <div class="func" ref="func" v-show="isShowFunc">
      <div class="image">
        <label for="file" style="width: 100px;height: 10px;">
          图片上传
          <input
            type="file"
            id="file"
            style="display: none"
            accept="image/jpg,image/jpeg,image/png,image/gif"
            multiple
            @change="imgChange"
          />
        </label>
<!--        展示表情-->
        <div @click="emojiClick" @keyup="emojiClick">选择表情</div>
      </div>
      <div class="tag" @click="tagBoxBounceUp" @keydown="tagBoxBounceUp">选择标签</div>
    </div>
<!--    标签-->
    <div class="isTageBox" v-show="isTagBox">
      <ul>
        <li @click="TagSelect('标签1')" @keyup="TagSelect('标签1')">标签1</li>
        <li @click="TagSelect('标签2')" @keyup="TagSelect('标签2')">标签2</li>

      </ul>
      <div @click="TagSelectOk" @keyup="TagSelectOk">完成</div>
    </div>
<!--    表情-->
    <Emoji v-if="showEmoji" @addEmoji="addEmoji"/>
<!--    裁剪图片-->
    <Cropper
      :isGif="isGif"
      :imageUrl="imageUrl"
      v-if="showCropper"
      @closeCropper="closeCropper"
      @imgCropped="imgCropped"
    />
<!--    预览图片-->
    <PhotoSwiper
      :isPrview="isPrview"
      :previewIndex="previewIndex"
      :previewImg="perImg"
      @changImageUrl="changImageUrl"
    />
  </div>
</template>

<script>
import Emoji from '@/components/Emoji.vue';
import Cropper from '@/components/Cropper.vue';
import PhotoSwiper from '@/components/PhotoSwipe.vue';

export default {
  name: 'HomeView',
  data() {
    return {
      isShowFunc: true, // 是否展示上传文件，表情，添加标签
      isTagBox: false, // 展示添加标签
      imageUrl: [], // 处理后图片存储区域
      textarea: '', // 编辑区内容
      showEmoji: false, // 展示表情
      showCropper: false, // 展示裁剪
      perImg: [], // 预览数组
      isPrview: false, // 预览开始
      previewIndex: 0, // 预览索引
      isGif: false, // 是否是gif类型
    };
  },
  components: {
    Emoji,
    Cropper,
    PhotoSwiper,
  },
  mounted() {
  },
  methods: {
    // 打开选择标签选项
    tagBoxBounceUp() {
      this.isTagBox = true;
    },
    // 选择完成关闭标签
    TagSelectOk() {
      this.isTagBox = false;
    },
    // 标签选择
    TagSelect(value) {
      this.textarea = `${this.textarea}#${value} `;
    },
    // 同步读取
    syncFile(file) {
      return new Promise((resolve, reject) => {
        const reader = new FileReader();

        reader.readAsDataURL(file);

        reader.onload = function (e) {
          resolve(e);
        };
        reader.onerror = () => {
          reject();
        };
      });
    },
    // 选择图片后的处理
    async imgChange(e) {
      const { files } = e.target;
      for (let i = 0; i < files.length; i++) {
        // const reader = new FileReader();
        // reader.readAsDataURL(files[i]);
        // console.log(files[i]);
        // eslint-disable-next-line no-await-in-loop
        const rederUrl = await this.syncFile(files[i]);
        console.log(rederUrl);
        const result = files[i].name.split('.')[1];
        if (result === 'gif') {
          this.isGif = true;
        } else {
          this.isGif = false;
        }
        if (this.imageUrl.length === 0) {
          this.imageUrl.push({
            id: rederUrl.loaded,
            url: rederUrl.target.result,
            isGif: this.isGif,
          });
        } else {
          const status = this.imageUrl.some((item) => item.id === rederUrl.loaded);
          if (!status) {
            this.imageUrl.push({
              url: rederUrl.target.result,
              id: rederUrl.loaded,
              isGif: this.isGif,
            });
          }
        }
      }
      // 准备裁剪
      this.showCropper = true;
    },
    // 点击图片进行预览操作
    imgClick(index) {
      console.log(this.$refs.img, index);
      // eslint-disable-next-line no-shadow
      this.$refs.img.forEach((item, index) => {
        this.perImg[index].w = item.offsetWidth;
        this.perImg[index].h = item.offsetHeight;
        this.perImg[index].completed = true;
      });
      this.previewIndex = index;
      this.isPrview = true;
    },
    // 展示表情
    emojiClick() {
      this.showEmoji = true;
    },
    /**
     * 选择表情组件传递的信息
     * 关闭表情组件
     */
    addEmoji(data) {
      this.textarea += data;
      this.showEmoji = false;
    },
    /**
     * 关闭裁剪组件
     */
    closeCropper() {
      this.showCropper = false;
    },
    /**
     * 裁剪后图片
     */
    imgCropped(data) {
      this.perImg.push({
        src: data.src,
        id: data.id,
      });
    },
    /**
     * 预览完毕后处理
     */
    changImageUrl(data) {
      this.perImg = data;
      this.isPrview = false;
    },
    /**
     * 关闭操作
     */
    close() {
      console.log('close');
    },
    /**
     * 上传操作
     */
    upload() {
      console.log(this.perImg);
      console.log(this.textarea);
    },
  },
};
</script>
<style lang="scss">
.home{
  width: 750px;
  padding: 0 10px;
  box-sizing: border-box;
  .top{
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .inputBox {
    width: 750px;
    margin: 0 auto;

    textarea {
      width: 730px;
      min-height: 300px;
      border: 1px solid #333;
      box-sizing: border-box;
    }
  }
  .image{
    width: 730px;
    height: 300px;
    display: flex;
    flex-wrap: wrap;
    img{
      width: 300px;
      height: 300px;
    }
  }

  .func {
    width: 730px;
    height: 150px;
    background-color: pink;
    position: fixed;
    left: 10px;
    bottom: 0;
    display: flex;
    justify-content: space-between;
    align-items: center;
    box-sizing: border-box;
    & > div {
      width: 100px;
      height: 50px;
    }
    .image{
      width: 200px;
      display: flex;
    }
  }

  .isTageBox {
    width: 750px;
    height: 300px;
    background-color: saddlebrown;
    position: fixed;
    bottom: 0;
    left: 0;
  }
}
</style>
