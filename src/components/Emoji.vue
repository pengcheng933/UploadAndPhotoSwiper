<template>
  <div class="emoji-wrap">
    <Swiper :autoPlay="false">
      <Slide v-for="(outerItem, outerIndex) in emojiList" :key="outerIndex">
        <div class="emoji-row" v-for="(item, index) in outerItem" :key="index">
          <span
            class="emoji"
            v-for="(innerItem, innerIndex) in item"
            :key="innerIndex"
            @click="addEmoji(item[innerIndex])"
            @keyup="addEmoji(item[innerIndex])"
          >
            {{ innerItem }}
          </span>
        </div>
      </Slide>
    </Swiper>
  </div>
</template>

<script>

// import {  } from "@/components" // ็ปไปถ
// import {  } from "@/fetch" // api
import { Swiper, Slide } from 'vue-swiper-component';

export default {
  name: 'emoji',
  data() {
    return {
      emojiList: {
        0: [
          ['๐', '๐', '๐', '๐', '๐', '๐', '๐'],
          ['๐คฃ', '๐', '๐', '๐', '๐', '๐', '๐'],
          ['๐', '๐', '๐', '๐', '๐', '๐', '๐'],
          ['๐', '๐', '๐ค', '๐', '๐', '๐', '๐'],
        ],
        1: [
          ['๐', '๐', '๐', '๐', '๐ฃ', '๐', '๐ซ'],
          ['๐ฉ', '๐ข', '๐ญ', '๐ค', '๐ ', '๐ก', '๐ณ'],
          ['๐ฑ', '๐จ', '๐ฐ', '๐ฅ', '๐', '๐ค', '๐ค'],
          ['๐คฅ', '๐ถ', '๐', '๐', '๐ฌ', '๐', '๐ฏ'],
        ],
        2: [
          ['๐ฆ', '๐ง', '๐ฎ', '๐ฒ', '๐ด', '๐คค', '๐ช'],
          ['๐ต', '๐ค', '๐คข', '๐คง', '๐ท', '๐ค', '๐ค'],
          ['๐ค', '๐ค ', '๐', '๐ฟ', '๐น', '๐บ', '๐คก'],
          ['๐ฉ', '๐ป', '๐', 'โ ๏ธ', '๐ฝ', '๐พ', '๐ค'],
        ],
        3: [
          ['๐', '๐บ', '๐ธ', '๐น', '๐ป', '๐ผ', '๐ฝ'],
          ['๐', '๐ฟ', '๐พ', '๐', '๐ค', '๐', 'โ'],
          ['๐', '๐', 'โ๏ธ', '๐ค', '๐ค', '๐ค', '๐'],
          ['๐', '๐', '๐', '๐', 'โ๏ธ', '๐', '๐'],
        ],
        4: [
          ['โ', '๐', '๐ค', '๐ค', '๐', '๐', '๐'],
          ['๐ค', '๐', 'โ๏ธ', '๐', '๐คณ', '๐ช', '๐ฃ'],
          ['๐', '๐', '๐', '๐', '๐', '๐', '๐'],
        ],
      },
    };
  },
  computed: {
  },
  components: {
    Swiper, Slide,
  },
  created() {
    //
  },
  activated() {
    //
  },
  mounted() {
    //
  },
  methods: {
    addEmoji(emoji) {
      this.$emit('addEmoji', emoji);
    },
  },
};

</script>

<style lang="less" scoped>

.emoji-wrap {
  position: absolute;
  width: 750px;
  bottom: 73px;
  height: 541px;
  background: #E8E8E8;

  & /deep/ .wh_content {
    height: 610px;
  }

  & /deep/ .wh_swiper {
    height: 610px;
  }

  & /deep/ .wh_slide {
    box-sizing: border-box;
    height: 610px;
    padding: 29.8px 20.8px 0;
  }

  & /deep/ .wh_show_bgcolor {
    background: #000;
    opacity: .55;
  }

  .emoji-row {
    display: flex;
    justify-content: space-between;
    padding-bottom: 18px;
  }

  .emoji {
    font-size: 69.4px;

  }
}

</style>
