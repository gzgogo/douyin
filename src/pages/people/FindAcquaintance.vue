<template>
  <div class="FindAcquaintance" id="FindAcquaintance">
    <div class="header">
      <back mode="light" @click="back"></back>
      <Indicator
          style="width: 50%;"
          tabStyleWidth="40%"
          :tabTexts="['发现朋友','熟人列表']"
          v-model:active-index="currentSlideItemIndex">
      </Indicator>
      <img src="../../assets/img/icon/menu-gray.png" alt="" class="option" @click="moreOptionDialog = true">
    </div>
    <SlideRowList v-model:active-index="currentSlideItemIndex">
      <SlideItem class="tab1" style="overflow: auto;">
        <div class="mr2r ml2r mt1r">
          <Search v-if="!isShowRightText"
                  placeholder="搜索用户名字/抖音号"
                  :is-show-right-text="false"
                  @click="isShowRightText = true">
            <img src="../../assets/img/icon/scan-gray.png" style="width: 1rem;transform: scale(1.5)"
                 @click.stop="$nav('/scan')">
          </Search>
          <Search v-else v-model="searchKey" :is-show-right-text="true" @notice="search" @clear="isSearch = false"></Search>
        </div>
        <div class="no-search" v-if="!isShowRightText">
          <div class="look-address-list" @click="findAddressListDialog = true">
            <img class="left" src="../../assets/img/icon/people/address-book.png" alt="">
            <div class="right">
              <div class="notice">
                <div class="text1">查看通讯录朋友</div>
                <div class="text2">看看有谁在抖音</div>
              </div>
              <back direction="right"></back>
            </div>
          </div>
          <div class="line"></div>
          <div class="title">
            朋友推荐
            <img src="../../assets/img/icon/about-gray.png" style="width: 1rem;margin-left: .2rem;">
          </div>
          <People v-for="item in friends.all " :people="item" mode="recommend"></People>
        </div>
        <div class="is-search" v-else>
          <div class="tooltip" v-if="searchKey && !isSearch">
            <img src="../../assets/img/icon/close.svg" style="width: 1rem;">
            搜索用户名字/抖音号：<span class="searchKey">{{ searchKey }}</span>
          </div>
          <!--          TODO -->
          <People v-if="isSearch" v-for="item in friends.all " :people="item" mode="recommend"></People>
        </div>
      </SlideItem>
      <SlideItem class="tab2" style="overflow: auto;">
        <Search placeholder="搜索用户备注或名字" class="mr20p ml20p mt10p"></Search>
        <div class="title">{{ friends.all.length }} 位朋友</div>
        <People v-for="item in friends.all " :people="item" mode="friend"></People>
        <NoMore class="mb5r"/>
      </SlideItem>
    </SlideRowList>

    <transition name="fade">
      <Mask v-if="maskDialog" @click="maskDialog = false"></Mask>
    </transition>

    <transition name="fade">
      <div v-if="findAddressListDialog" class="find-address-list-dialog" @click="findAddressListDialog = false">
        <div class="body">
          <div>
            <img src="../../assets/img/icon/head-image.jpeg" alt="">
          </div>
          <span class="title">发现通讯录好友</span>
          <span class="desc">
            <span>授权通讯录，看看哪些好友在使用抖音。具体使用场景及撤回授权方式详见</span>
            <span class="link" @click="$nav('/service-protocol',{type:'“抖音”用户服务协议'})">《隐私政策》</span>
           </span>
        </div>
        <div class="footer">
          <div @click="findAddressListDialog = false">暂时不要</div>
          <div @click="$nav('/address-list')">发现好友</div>
        </div>
      </div>
    </transition>

    <from-bottom-dialog
        page-id="FindAcquaintance"
        v-model="moreOptionDialog"
        :show-heng-gang="false"
        height="21rem"
        mode="white">
      <div class="more-option-dialog">
        <div class="row" @click="outWebImgAccountDialog = true;moreOptionDialog = false">
          <span>站外好友口令</span>
        </div>
        <div class="row" @click="$nav('/scan')">
          <span>扫一扫加好友</span>
        </div>
        <div class="row" style="border-bottom: none;" @click="$nav('/face-to-face')">
          <span>面对面加好友</span>
        </div>
        <div class="space"></div>
        <div class="row" @click="moreOptionDialog = false">
          取消
        </div>
      </div>
    </from-bottom-dialog>

    <transition name="fade">
      <div class="out-web-img-account-dialog" v-if="outWebImgAccountDialog">
        <img src="../../assets/img/icon/head-image.jpeg" alt="" class="img-account">
        <img src="../../assets/img/icon/close-white.png" alt="" class="close" @click="outWebImgAccountDialog = false">
        <div class="desc">
          <div>这是你的图片口令</div>
          <div>你可以通过此口令添加站外好友</div>
        </div>
        <div class="notice">
          <img src="../../assets/img/icon/close.svg" alt="">
          <span>图片口令已保存到相册</span>
        </div>
        <div class="btn wechat" @click="outWebImgAccountDialog = false">
          <img src="../../assets/img/icon/close.svg" alt="">
          <span>发给微信好友</span>
        </div>
        <div class="btn qq" @click="outWebImgAccountDialog = false">
          <img src="../../assets/img/icon/close.svg" alt="">
          <span>发给QQ好友</span>
        </div>
      </div>
    </transition>

  </div>
</template>
<script>
import People from './components/People'
import Search from '../../components/Search'
import Indicator from '../../components/slide/Indicator'
import FromBottomDialog from "../../components/dialog/FromBottomDialog";
import {mapState} from "vuex";

export default {
  name: "FindAcquaintance",
  components: {
    People,
    Search,
    Indicator,
    FromBottomDialog
  },
  data() {
    return {
      findAddressListDialog: false,
      moreOptionDialog: false,
      outWebImgAccountDialog: false,
      indicatorFixed: false,
      isShowRightText: false,
      isSearch: false,
      searchKey: '',

      currentSlideItemIndex: 0,
      list: [
        {
          type: 1,
        },
        {
          type: 2,
        },
        {
          type: 3,
        },
        {
          type: 4,
        },
        {
          type: 5,
        },
      ],
    }
  },
  computed: {
    maskDialog: {
      get() {
        return this.findAddressListDialog || this.outWebImgAccountDialog
      },
      set() {
        this.findAddressListDialog = this.outWebImgAccountDialog = false
      }
    },
    ...mapState(['friends'])
  },
  mounted() {
  },
  methods: {
    async search() {
      this.$showLoading()
      await this.$sleep(500)
      this.$hideLoading()
      this.isSearch = true
    },
    back() {
      if (this.isShowRightText) {
        this.isShowRightText = false
      } else {
        this.$back()
      }
    }
  }
}
</script>

<style scoped lang="less">
@import "../../assets/less/index";

.from-bottom-enter-active,
.from-bottom-leave-active {
  transition: transform 0.2s ease;
}

.from-bottom-enter-from,
.from-bottom-leave-to {
  transform: translate3d(0, 50vh, 0);
}

.FindAcquaintance {
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  top: 0;
  color: white;
  font-size: 1.4rem;

  .header {
    height: 6rem;
    font-size: 1.4rem;
    padding: 0 2rem;
    display: flex;
    justify-content: space-between;
    align-items: center;
    border-bottom: 1px solid @line-color;

    img {
      height: 2rem;
    }
  }

  .tab2 {
    box-sizing: border-box;
    padding: 2rem;

    .title {
      margin-top: @padding-page;
      margin-bottom: 1rem;
      color: @second-text-color;
      font-size: 1.2rem;
    }
  }

  .tab1 {
    .title {
      display: flex;
      align-items: center;
      margin-top: 2rem;
      margin-bottom: 1rem;
      color: @second-text-color;
      font-size: 1.2rem;

      img {
        width: 1rem;
      }
    }

    .no-search {
      padding: 2rem;

      .look-address-list {
        margin: 0 0 2rem 0;
        display: flex;
        justify-content: space-between;
        align-items: center;

        .left {
          background: @second-btn-color-tran;
          border-radius: 50%;
          padding: 1.2rem;
          width: 2.2rem;
          margin-right: 1.5rem;
        }

        .right {
          flex: 1;
          display: flex;
          justify-content: space-between;
          align-items: center;

          img {
            width: 1.4rem;
          }

          .notice {
            .text1 {
              font-size: 1.4rem;
              margin-bottom: .5rem;
            }

            .text2 {
              font-size: 1.2rem;
              color: @second-text-color;
            }
          }
        }
      }
    }

    .is-search {
      padding: 0 2rem 2rem 2rem;

      .tooltip {
        font-size: 1.2rem;
        margin-top: 2rem;
        display: flex;
        align-items: center;
        color: @second-text-color;

        img {
          margin-right: 1rem;
          width: 1.5rem;
        }

        .searchKey {
          color: yellow;
        }
      }
    }
  }

  .find-address-list-dialog {
    z-index: 4;
    position: fixed;
    left: 50%;
    top: 50%;
    transform: translate3d(-50%, -50%, 0);
    width: 70vw;
    color: black;
    background: white;
    box-sizing: border-box;
    border-radius: .3rem;
    display: flex;
    flex-direction: column;
    align-items: center;

    .body {
      padding: 2rem;
      display: flex;
      flex-direction: column;
      align-items: center;

      img {
        height: 6rem;
        margin-bottom: 2rem;
      }

      .title {
        font-size: 1.6rem;
        font-weight: bold;
        margin-bottom: 1rem;
      }

      .desc {
        font-size: 1rem;
        color: gray;
      }

      .link {
        color: rgb(18, 100, 149);
      }
    }

    @footer-border-color: #eaeaf1;

    .footer {
      width: 100%;
      display: flex;
      height: 4rem;
      border-top: 1px solid @footer-border-color;
      font-size: 1.4rem;

      div {
        display: flex;
        align-items: center;
        justify-content: center;
        width: 49%;
        text-align: center;
        border-right: 1px solid @footer-border-color;
        opacity: .5;

        &:nth-last-child(1) {
          border-right: none;
          opacity: 1;
        }
      }
    }

  }

  .more-option-dialog {
    font-size: 1.4rem;

    .space {
      height: 1rem;
      background: whitesmoke;
    }

    .row {
      height: 5rem;
      color: black;
      background: white;
      box-sizing: border-box;
      width: 100%;
      //border-bottom: 1px solid ghostwhite;
      border-bottom: 1px solid gainsboro;
      display: flex;
      align-items: center;
      justify-content: center;
    }
  }

  .out-web-img-account-dialog {
    z-index: 4;
    position: fixed;
    left: 50%;
    top: 50%;
    transform: translate3d(-50%, -50%, 0);
    width: 80vw;
    color: black;
    background: white;
    box-sizing: border-box;
    border-radius: .3rem;
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 2rem;

    .img-account {

    }

    .close {
      top: 1rem;
      right: 1rem;
      position: absolute;
      background: @second-btn-color-tran;
      padding: .4rem;
      width: 1rem;
      border-radius: 50%;
    }

    .desc {
      margin-top: 2rem;
      text-align: center;

    }

    .notice {
      margin-top: 2rem;
      display: flex;
      align-items: center;
      color: @second-text-color;

      img {
        margin-right: 1rem;
        width: 1rem;
      }
    }

    .btn {
      width: 100%;
      height: 4rem;
      border-radius: .3rem;
      margin-top: 2rem;
      display: flex;
      justify-content: center;
      align-items: center;
      color: white;

      &:nth-last-child(1) {
        margin-top: 1rem;
      }

      &.wechat {
        background: rgb(62, 174, 56);
      }

      &.qq {
        background: rgb(24, 183, 238);
      }

      img {
        margin-right: 1rem;
        width: 1rem;
      }
    }
  }
}
</style>
