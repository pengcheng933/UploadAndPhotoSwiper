<template>
  <div class="photoPreview-wrap">
    <div class="pswp" tabindex="-1" role="dialog" aria-hidden="false" ref="pswp">
  <!-- Background of PhotoSwipe.
       It's a separate element as animating opacity is faster than rgba(). -->
  <div class="pswp__bg"></div>

  <!-- Slides wrapper with overflow:hidden. -->
  <div class="pswp__scroll-wrap">

    <!-- Container that holds slides.
        PhotoSwipe keeps only 3 of them in the DOM to save memory.
        Don't modify these 3 pswp__item elements, data is added later on. -->
    <div class="pswp__container">
      <div class="pswp__item"></div>
      <div class="pswp__item"></div>
      <div class="pswp__item"></div>
    </div>

    <!-- Default (PhotoSwipeUI_Default) interface on top of sliding area. Can be changed. -->
    <div class="pswp__ui pswp__ui--hidden">

      <div class="pswp__top-bar">

        <!--  Controls are self-explanatory. Order can be changed. -->

        <div class="pswp__counter"></div>

        <button
          style="float:left;"
          class="pswp__button pswp__button--close"
          title="Close (Esc)"
        ></button>

        <!-- <button class="pswp__button pswp__button--share" title="Share"></button> -->

        <!-- <button class="pswp__button pswp__button--fs" title="Toggle fullscreen"></button> -->

        <button  class="pswp__button pswp__button--zoom" title="Zoom in/out"></button>

        <!-- Preloader demo https://codepen.io/dimsemenov/pen/yyBWoR -->
        <!-- element will get class pswp__preloader--active when preloader is running -->
        <div class="pswp__preloader">
          <div class="pswp__preloader__icn">
            <div class="pswp__preloader__cut">
              <div class="pswp__preloader__donut"></div>
            </div>
          </div>
        </div>
      </div>

      <div class="pswp__share-modal pswp__share-modal--hidden pswp__single-tap">
        <div class="pswp__share-tooltip"></div>
      </div>

      <button class="pswp__button pswp__button--arrow--left" title="Previous (arrow left)">
      </button>

      <button class="pswp__button pswp__button--arrow--right" title="Next (arrow right)">
      </button>

      <div class="pswp__caption">
        <div class="pswp__caption__center"></div>
      </div>

    </div>

  </div>
  <button
    class="myBtn"
    @pointerdown.prevent
    @click.stop="checkboxClick"
    @keyup="checkboxClick"
    :class="isactive?'isActive':'noActive'"
  ></button>
  </div>
  </div>
</template>

<script>
import PhotoSwiper from 'photoswipe/dist/photoswipe';
import UI from 'photoswipe/dist/photoswipe-ui-default';
import 'photoswipe/dist/photoswipe.css';
import 'photoswipe/dist/default-skin/default-skin.css';

export default {
  name: 'photoSwiper',
  data() {
    return {
      // items: [],
      gallery: null,
      isactive: true, // 右上角是否被选中
    };
  },
  props: {
    isPrview: {
      type: Boolean,
    }, // 是否在预览（也是触发预览的开关）
    previewIndex: {
      type: Number,
    }, // 预览索引
    previewImg: {
      type: Array,
    }, // 预览img
  },
  watch: {
    isPrview(val) {
      if (val === true) this.initPhotoSwiper();
    },
  },
  methods: {
    initPhotoSwiper() {
      const { pswp } = this.$refs;
      const options = {
        index: this.previewIndex,
      };
      this.gallery = new PhotoSwiper(pswp, UI, this.previewImg, options);
      this.gallery.init();
      /**
       * 关闭按钮
       * 过滤调没选中的
       */
      this.gallery.listen('close', () => {
        const info = this.previewImg.filter((item) => item.completed).map((item) => ({
          src: item.src,
          id: item.id,
        }));
        console.log(info);
        this.$emit('changImageUrl', info);
      });
      /**
       * 点击下一张时获取对应状态
       */
      this.gallery.listen('beforeChange', () => {
        this.isactive = this.previewImg[this.gallery.getCurrentIndex()].completed;
      });
    },
    checkboxClick() {
      /**
       * 切换是否选中状态
       */
      // eslint-disable-next-line max-len,vue/no-mutating-props
      this.previewImg[this.gallery.getCurrentIndex()].completed = !this.previewImg[this.gallery.getCurrentIndex()].completed;
      this.isactive = this.previewImg[this.gallery.getCurrentIndex()].completed;
    },
  },
};
</script>

<style lang="scss" scoped>
.photoPreview-wrap {
.myBtn{
  width: 20px;
  height: 20px;
  position: fixed;
  top:10px;
  left:681.2px;

}
.isActive{
  background-color: pink;
}
.noActive{
  background-color: white;

}
.pswp__button–close{
  margin-top: 27px;
}
.pswp__counter{
  margin-top: 29.1px;
  margin-left: 352.7px;
}
}
</style>
