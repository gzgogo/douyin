<template>
  <div id='BaseHeader' :class="[mode,isFixed?'fixed':'']">
    <div class="header">
      <back
          :mode="backMode"
          :img="backImg"
          @click="back()"
          class="left"
          direction="left"/>
      <slot name="center"><span></span></slot>
      <slot name="right"><span></span></slot>
    </div>
    <slot name="bottom"></slot>
  </div>
</template>
<script>
export default {
  name: "BaseHeader",
  components: {},
  props: {
    mode: {
      type: String,
      default: 'dark'
    },
    backMode: {
      type: String,
      default: 'gray'
    },
    backImg: {
      type: String,
      default: 'back',
    },
    isClose: {
      type: Boolean,
      default: false,
    },
    isFixed: {
      type: Boolean,
      default: true,
    }
  },
  data() {
    return {}
  },
  created() {
  },
  computed: {},
  methods: {
    back() {
      if (this.$attrs.onBack) {
        this.$attrs.onBack()
      } else {
        this.$back()
      }
    }
  }
}
</script>

<style scoped lang="less">
@import "../assets/less/index";

#BaseHeader {
  width: 100%;

  &.fixed {
    z-index: 2;
    top: 0;
    position: fixed;
  }

  &.light {
    background: white;
    color: black;
  }

  &.dark {
    background: @main-bg;
    color: white;
  }

  .header {
    display: flex;
    justify-content: center;
    align-items: center;
    height: @header-height;
    box-sizing: border-box;
    border-bottom: 1px solid #cccccc11;
    position: relative;

    .left {
      position: absolute;
      left: 10rem;
      top: 20rem;
    }

    & > :nth-last-child(1) {
      height: 100%;
      position: absolute;
      right: 17px;
      top: 0;
      display: flex;
      align-items: center;
    }
  }
}
</style>
