<template>
  <div class="index">
    <BaseHeader>
      <template v-slot:center>
        <span class="f16">未成年保护工具</span>
      </template>
    </BaseHeader>
    <div class="content">
      <div class="row" @click="$nav('detail-setting',{type:0})">
        <div class="left">时间锁</div>
        <div class="right">
          <span>未开启</span>
          <back direction="right"></back>
        </div>
      </div>
      <div class="row" @click="$nav('detail-setting',{type:1})">
        <div class="left">青少年模式</div>
        <div class="right">
          <span>未开启</span>
          <back direction="right"></back>
        </div>
      </div>
      <div class="row" @click="$nav('detail-setting',{type:2})">
        <div class="left">亲子平台</div>
        <div class="right">
          <span>未开启</span>
          <back direction="right"></back>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: "index",
  data() {
    return {}
  },
  computed: {},
  created() {
  },
  methods: {}
}
</script>

<style scoped lang="less">
@import "../../../../assets/less/index";

.index {
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  top: 0;
  overflow: auto;
  color: white;
  font-size: 1.4rem;

  .content {
    padding-top: 6rem;
  }
}
</style>
