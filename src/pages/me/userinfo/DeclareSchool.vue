<template>
  <div class="declare-school">
    <BaseHeader>
      <template v-slot:center>
        <span class="f16">申报学校信息</span>
      </template>
    </BaseHeader>
    <div class="content">
      <div class="row">
        <div class="label">学校全称</div>
        <input type="text" placeholder="请输入学校全称(必填)" v-model="form.name">
      </div>
      <div class="row" v-if="type === 1">
        <div class="label">所在城市</div>
        <input type="text" placeholder="请输入学校所在城市(必填)" v-model="form.location">
      </div>
      <div class="department-row" v-if="type === 2">
        <div class="label">信息问题</div>
        <div class="right">
          <span>点击选择(必选)</span>
          <img src="../../../assets/img/icon/back.png" alt="">
        </div>
      </div>
      <div class="notice">感谢你的反馈，我们将尽快核对信息</div>
      <div class="btn" @click="submit">提交</div>
    </div>
  </div>
</template>

<script>
//TODO　院系点击那个弹窗没做
export default {
  name: "DeclareSchool",
  data() {
    return {
      form: {
        name: '',
        location: '',
        departmentInfoType: ''
      },
      type: 1
    }
  },
  created() {
    this.type = Number(this.$route.query.type)
  },
  methods: {
    submit() {
      if (!this.form.name) return this.$notice('请输入学校全称')
      if (this.type === 1 && !this.form.location) return this.$notice('请输入学校所在城市')
      if (this.type === 2 && !this.form.departmentInfoType) return this.$notice('请选择信息问题')
      this.$notice('申报成功')
      setTimeout(this.$back, 1000)
    },

  }
}
</script>

<style scoped lang="less">
@import "../../../assets/less/index";

.declare-school {
  position: fixed;
  top: 0;
  bottom: 0;
  right: 0;
  left: 0;


  .content {
    padding: 7rem 2rem 0 2rem;

    .row {
      display: flex;
      align-items: center;

      .label {
        color: white;
        font-size: 1.6rem;
        margin-right: 3rem;
      }

      input {
        flex: 1;
        height: 4rem;
        background: transparent;
        outline: none;
        border-top: none;
        border-right: none;
        border-left: none;
        border-bottom: 1px solid @line-color;
        color: white;
      }
    }

    .department-row {
      display: flex;
      align-items: center;
      justify-content: space-between;

      .label {
        color: white;
        font-size: 1.6rem;
        margin-right: 3rem;
      }

      .right {
        padding: 1.2rem 0;
        display: flex;
        align-items: center;
        color: @second-text-color;

        img {
          margin-left: 5px;
          width: 1.2rem;
        }
      }


    }

    .notice {
      margin-top: 2rem;
      color: @second-text-color;
    }

    .btn {
      position: fixed;
      bottom: 2rem;
      left: 2rem;
      right: 2rem;
      color: white;
      font-size: 1.6rem;
      background: @primary-btn-color;
      text-align: center;
      padding: 1rem 0;
      border-radius: 2px;
    }
  }
}

</style>