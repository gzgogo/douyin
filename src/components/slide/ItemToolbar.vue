<script setup>
import BaseMusic from "../BaseMusic";
import Utils from "../../utils";
import {reactive,} from "vue";

const props = defineProps({
  item: {
    type: Object,
    default: () => {
      return {}
    }
  },
  position: {
    type: Object,
    default: () => {
      return {}
    }
  },
  isMy: {
    type: Boolean,
    default: () => {
      return false
    }
  },
})
const emit = defineEmits([
  'update:item',
  'goUserInfo',
  'showComments',
  'showShare',
  'goMusic',
])
const state = reactive({})

function loved() {
  Utils.updateItem(props, 'isLoved', !props.item.isLoved, emit)
}

function attention(e) {
  e.currentTarget.classList.add('attention')
  setTimeout(() => {
    Utils.updateItem(props, 'isAttention', true, emit)
  }, 1000)
}

</script>

<template>
  <div class="toolbar mb1r">
    <div class="avatar-ctn mb4r">
      <img class="avatar" :src="props.item.author.avatar" alt=""
           @click.stop="$emit('goUserInfo')">
      <transition name="fade">
        <div v-if="!props.item.isAttention" @click.stop="attention" class="options">
          <img class="no" src="../../assets/img/icon/add-light.png" alt="">
          <img class="yes" src="../../assets/img/icon/ok-red.png" alt="">
        </div>
      </transition>

    </div>
    <div class="love mb2r" @click.stop="loved($event)">
      <div>
        <img src="../../assets/img/icon/love.svg" class="love-image" v-if="!props.item.isLoved">
        <img src="../../assets/img/icon/loved.svg" class="love-image" v-if="props.item.isLoved">
      </div>
      <span>{{ Utils.formatNumber(props.item.digg_count) }}</span>
    </div>
    <div class="message mb2r" @click.stop="$emit('showComments')">
      <img src="../../assets/img/icon/message.svg" alt="" class="message-image">
      <span>{{ Utils.formatNumber(props.item.comment_count) }}</span>
    </div>
    <div class="message mb2r" @click.stop="$emit('showComments')">
      <img src="../../assets/img/icon/star-white.png" alt="" class="message-image">
      <span>{{ Utils.formatNumber(props.item.comment_count) }}</span>
    </div>
    <div v-if="!isMy" class="share mb4r" @click.stop="$emit('showShare')">
      <img src="../../assets/img/icon/share-white-full.png" alt="" class="share-image">
      <span>{{ Utils.formatNumber(props.item.share_count) }}</span>
    </div>
    <div v-else class="share mb4r" @click.stop="$emit('showShare')">
      <img src="../../assets/img/icon/menu-white.png" alt="" class="share-image">
    </div>
    <!--    <BaseMusic-->
    <!--        :cover="props.item.music.cover"-->
    <!--        @click.stop="$nav('/home/music')"-->
    <!--    /> -->
    <BaseMusic
        :cover="props.item.music.cover"
    />
  </div>
</template>

<style scoped lang="less">
.toolbar {
  //width: 40px;
  position: absolute;
  bottom: 0;
  right: 5px;
  color: #fff;

  .avatar-ctn {
    position: relative;

    .avatar {
      width: 55px;
      height: 55px;
      border-radius: 50%;
    }

    .options {
      position: absolute;
      border-radius: 50%;
      margin: auto;
      left: 0;
      right: 0;
      bottom: -5px;
      background: red;
      //background: black;
      width: 18px;
      height: 18px;
      display: flex;
      justify-content: center;
      align-items: center;
      transition: all 1s;

      img {
        position: absolute;
        width: 12px;
        height: 12px;
        transition: all 1s;
      }

      .yes {
        opacity: 0;
        transform: rotate(-180deg);
      }

      &.attention {
        background: white;

        .no {
          opacity: 0;
          transform: rotate(180deg);
        }

        .yes {
          opacity: 1;
          transform: rotate(0deg);
        }
      }
    }
  }

  .love, .message, .share {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;

    @width: 35rem;

    img {
      width: @width;
      height: @width;
    }

    span {
      font-size: 12rem;
    }
  }

  .loved {
    background: red;
  }

}
</style>