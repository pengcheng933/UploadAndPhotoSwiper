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

// import {  } from "@/components" // 组件
// import {  } from "@/fetch" // api
import { Swiper, Slide } from 'vue-swiper-component';

export default {
  name: 'emoji',
  data() {
    return {
      emojiList: {
        0: [
          ['😀', '😃', '😄', '😁', '😆', '😅', '😂'],
          ['🤣', '😊', '😇', '🙂', '🙃', '😉', '😌'],
          ['😍', '😘', '😗', '😙', '😚', '😋', '😛'],
          ['😝', '😜', '🤓', '😎', '😏', '😒', '😞'],
        ],
        1: [
          ['😔', '😟', '😕', '🙁', '😣', '😖', '😫'],
          ['😩', '😢', '😭', '😤', '😠', '😡', '😳'],
          ['😱', '😨', '😰', '😥', '😓', '🤗', '🤔'],
          ['🤥', '😶', '😐', '😑', '😬', '🙄', '😯'],
        ],
        2: [
          ['😦', '😧', '😮', '😲', '😴', '🤤', '😪'],
          ['😵', '🤐', '🤢', '🤧', '😷', '🤒', '🤕'],
          ['🤑', '🤠', '😈', '👿', '👹', '👺', '🤡'],
          ['💩', '👻', '💀', '☠️', '👽', '👾', '🤖'],
        ],
        3: [
          ['🎃', '😺', '😸', '😹', '😻', '😼', '😽'],
          ['🙀', '😿', '😾', '👋', '🤚', '🖐', '✋'],
          ['🖖', '👌', '✌️', '🤞', '🤘', '🤙', '👈'],
          ['👉', '👆', '🖕', '👇', '☝️', '👍', '👎'],
        ],
        4: [
          ['✊', '👊', '🤛', '🤜', '👏', '🙌', '👐'],
          ['🤝', '🙏', '✍️', '💅', '🤳', '💪', '👣'],
          ['👂', '👃', '👀', '👁', '👅', '👄', '💋'],
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
