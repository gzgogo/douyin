<template>
  <div class="MyMusic">
    <div class="header">
      <back class="back" mode="light" img="back" @click="$back"/>
      <IndicatorLight
          name="myMusicList"
          :tabTexts="['猜你爱听','我的收藏']"
          v-model:active-index="slideIndex">
      </IndicatorLight>
      <back style="opacity: 0;" mode="light" img="back"/>
    </div>
    <SlideRowList name="myMusicList" v-model:active-index="slideIndex">
      <SlideItem>
        <GuessMusic :list="guessMusic"/>
      </SlideItem>
      <SlideItem style="overflow: auto;">
        <Loading style="left: 150%;" v-if="loading"/>
        <div v-else class="my-collect">
          <div class="wrapper">
            <div class="play-all">
              <div class="left">
                <img src="../../assets/img/icon/me/play-all.webp" alt="">
                <span>播放全部</span>
                <span class="num">(2)</span>
              </div>
              <img class="menu" src="../../assets/img/icon/menu-white.png" alt="">
            </div>
            <div class="collect-list">
              <div class="item" v-for="(item,index) in collectMusic" @click="page2PlayMusic(item)">
                <div class="left">
                  <div class="cover-wrapper">
                    <img v-lazy="$imgPreview(item.cover)" alt="" class="cover">
                  </div>
                  <div class="desc">
                    <span class="name">{{ item.name }}</span>
                    <div class="author">{{ item.author }}</div>
                    <div class="desc-bottom">
                      <div class="tag">完整版</div>
                      <div class="duration">{{ $duration(item.duration) }}</div>
                    </div>
                  </div>
                </div>
                <div class="right">
                  <img v-if="page2SlideIndex === index" class="playing-icon"
                       src="../../assets/img/icon/me/pinlv.gif">
                </div>
              </div>
            </div>
            <div class="recommend">
              <span>推荐收藏</span>
              <div class="right">
                <span class="auto-play">自动播放</span>
                <switches v-model="isAutoPlay" theme="bootstrap" color="success"></switches>
              </div>
            </div>
            <div class="recommend-list">
              <div class="item" v-for="(item,index) in recommendMusic" @click="page2PlayMusic(item)">
                <div class="left">
                  <div class="cover-wrapper">
                    <img v-lazy="$imgPreview(item.cover)" alt="" class="cover">
                  </div>
                  <div class="desc">
                    <span class="name">{{ item.name }}</span>
                    <div class="author">{{ item.author }}</div>
                    <div class="desc-bottom">
                      <div class="tag">完整版</div>
                      <div class="duration">{{ $duration(item.duration) }}</div>
                    </div>
                  </div>
                </div>
                <div class="right">
                  <img v-if="page2SlideIndex - collectMusic.length === index" class="playing-icon"
                       src="../../assets/img/icon/me/pinlv.gif">
                  <div class="collect-icon">
                    <img src="../../assets/img/icon/star-white.png" v-show="!item.isCollect"
                         @click.stop="item.isCollect = !item.isCollect">
                    <img src="../../assets/img/icon/star-yellow.png" v-show="item.isCollect"
                         @click.stop="item.isCollect = !item.isCollect">
                  </div>
                </div>
              </div>
            </div>
          </div>
          <transition name="float-play">
            <div v-if="isShowFloatPlay" class="playing" @click="isShowCollectDialog = true">
              <div class="playing-wrapper">
                <div class="cover-wrapper">
                  <img v-lazy="$imgPreview(currentMusic.cover)" alt="" class="cover">
                </div>
                <div class="name">{{ currentMusic.name }}</div>
                <img v-show="page2IsPlay" @click.stop="togglePage2Play" class="option"
                     src="../../assets/img/icon/me/float-pause-one.png" alt="">
                <img v-show="!page2IsPlay" @click.stop="togglePage2Play" class="option"
                     src="../../assets/img/icon/me/float-play.png" alt="">
                <img @click.stop="$no" class="menu-list" src="../../assets/img/icon/me/music-list.png" alt="">
              </div>
            </div>
          </transition>
        </div>
      </SlideItem>
    </SlideRowList>
    <transition name="my-collect-dialog">
      <div class="my-collect-dialog" v-show="isShowCollectDialog">
        <div class="dialog-header">
          <back class="close" mode="light" img="back" @click="isShowCollectDialog = false"/>
          <span>我的收藏</span>
          <back style="opacity: 0;" mode="light" img="back"/>
        </div>
        <CollectMusic ref="CollectMusic" :list="page2Music" v-model:page2SlideIndex="page2SlideIndex"/>
      </div>
    </transition>
  </div>
</template>
<script>
import {mapState} from "vuex";
import Switches from "../message/components/swtich/switches";
import SlideItemMusic from "./components/SlideItemMusic";
import IndicatorLight from "../../components/slide/IndicatorLight";
import FromBottomDialog from "../../components/dialog/FromBottomDialog";
import GuessMusic from "./components/GuessMusic";
import CollectMusic from "./components/CollectMusic";
import Loading from "../../components/Loading";

//TODO 两个page页面的播放冲突未做
export default {
  name: "MyMusic",
  components: {
    FromBottomDialog,
    Switches,
    SlideItemMusic,
    IndicatorLight,
    GuessMusic,
    CollectMusic,
    Loading
  },
  data() {
    return {
      loading: false,
      slideIndex: 1,
      currentMusic: {
        name: '告白气球',
        mp3: 'https://mp32.9ku.com/upload/128/2017/02/05/858423.mp3',
        cover: new URL('../../assets/img/music-cover/7.png', import.meta.url).href,
        author: '周杰伦',
        duration: 60,
        use_count: 37441000,
        is_collect: false,
        is_play: false,
      },
      collectMusic: [],
      recommendMusic: [],
      guessMusic: [],

      isShowCollectDialog: false,
      isShowFloatPlay: false,

      isAutoPlay: true,
      isCollect: false,

      page2SlideIndex: -1,
      page2IsPlay: false,
    }
  },
  computed: {
    ...mapState(['bodyWidth']),
    page2Music() {
      return this.collectMusic.concat(this.recommendMusic)
    }
  },
  created() {
    this.getCollectMusic()
  },
  methods: {
    togglePage2Play() {
      this.page2IsPlay = !this.page2IsPlay
      if (this.page2IsPlay) {
        this.$refs.CollectMusic.play(this.page2SlideIndex)
      } else {
        this.$refs.CollectMusic.pause()
      }
    },
    page2PlayMusic(item) {
      this.currentMusic = item
      this.isShowFloatPlay = true
      this.page2IsPlay = true
      this.page2SlideIndex = this.page2Music.findIndex(v => v.name === item.name)
      this.isShowCollectDialog = true
      this.$refs.CollectMusic.play(this.page2SlideIndex)
    },
    async getCollectMusic() {
      this.loading = true
      let res = await this.$api.videos.collect()
      this.loading = false
      if (res.code === this.SUCCESS) {
        this.collectMusic = res.data.music.list.slice(0, 2)
        this.guessMusic = this.recommendMusic = res.data.music.list.slice(2, -1)
      }
    },
  }
}
</script>

<style scoped lang="less">
@import "../../assets/less/index";

.MyMusic {
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  top: 0;
  overflow: auto;
  color: white;
  font-size: 1.4rem;

  .header {
    z-index: 9;
    position: fixed;
    width: 100vw;
    top: 0;
    height: 5rem;
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 0 @padding-page;
    box-sizing: border-box;

    .back {
      z-index: 10;
    }

    .indicator-ctn {
      width: 60vw;
    }

  }

  .my-collect {
    margin-top: 5rem;
    color: rgba(88, 88, 96);
    position: relative;

    .wrapper {
      padding: @padding-page;
      padding-bottom: 8rem;
    }

    .play-all {
      margin-bottom: 2rem;
      display: flex;
      justify-content: space-between;
      align-items: center;
      color: white;

      .left {
        display: flex;
        align-items: center;

        img {
          width: 3rem;
          margin-right: 1rem;
        }

        .num {
          font-size: 1.3rem;
          color: gray;
          margin-left: .5rem;
        }
      }

      .menu {
        height: 2rem;
      }
    }

    .collect-list, .recommend-list {
      .item {
        color: white;
        display: flex;
        justify-content: space-between;
        margin-bottom: 1.5rem;

        .left {
          display: flex;

          .cover-wrapper {
            margin-right: 1rem;
            position: relative;
            display: flex;
            align-items: center;
            justify-content: center;

            .cover {
              border-radius: .2rem;
              @width: 6rem;
              width: @width;
              object-fit: cover;
              height: @width;
            }
          }

          .desc {
            display: flex;
            flex-direction: column;
            justify-content: space-between;

            .name {
              white-space: nowrap;
              text-overflow: ellipsis;
              overflow: hidden;
              max-width: 40vw;
            }

            .author, .desc-bottom {
              font-size: 1.2rem;
              color: @second-text-color;
            }

            .desc-bottom {
              display: flex;
              align-items: center;

              .tag {
                font-size: 1rem;
                background: @second-btn-color-tran;
                padding: .2rem .5rem;
                margin-right: .5rem;
              }

              .duration {
                margin-right: 1.4rem;
                position: relative;
              }
            }
          }
        }

        .right {
          display: flex;
          align-items: center;

          .playing-icon {
            width: 2.4rem;
          }

          .collect-icon {
            margin-left: 3rem;

            img {
              width: 2.4rem;
            }
          }
        }

      }
    }

    .recommend {
      color: white;
      margin: 3rem 0;
      display: flex;
      align-items: center;
      justify-content: space-between;

      .right {
        display: flex;
        align-items: center;
        justify-content: space-between;

        .auto-play {
          font-size: 1.3rem;
          color: @second-text-color;
          margin-right: 1rem;
        }

      }
    }

    .playing {
      padding: 0 @padding-page;
      box-sizing: border-box;
      position: fixed;
      bottom: 0;
      width: 100vw;
      color: white;
      background: rgba(56, 59, 68);

      .playing-wrapper {
        transform: translateY(-1rem);
        display: flex;
        justify-content: space-between;
        align-items: center;
      }

      .cover-wrapper {
        background: rgba(56, 59, 68);
        padding: .7rem;
        border-radius: 50%;

        .cover {
          background: rgba(97, 98, 103);
          padding: .3rem;
          @width: 5rem;
          height: @width;
          width: @width;
          object-fit: cover;
          border-radius: 50%;
        }
      }

      .name {
        margin: 0 1rem;
        flex: 1;

      }

      .option {
        width: 3.8rem;
        height: 3.8rem;
        margin-right: 2rem;
      }

      .menu-list {
        width: 2.8rem;
        height: 2.8rem;
      }
    }
  }

  .my-collect-dialog {
    position: fixed;
    z-index: 11;
    width: 100vw;
    height: 100vh;
    top: 0;
    background: rgb(136, 132, 133);

    .dialog-header {
      z-index: 9;
      font-size: 1.6rem;
      position: fixed;
      top: 0;
      width: 100vw;
      padding: @padding-page;
      box-sizing: border-box;
      height: 5rem;
      display: flex;
      align-items: center;
      justify-content: space-between;

      .close {
        transform: rotate(-90deg) !important;
      }
    }
  }

  .my-collect-dialog-enter-active,
  .my-collect-dialog-leave-active {
    transition-duration: 300ms;
    transform: translateY(0);
  }

  .my-collect-dialog-enter-from,
  .my-collect-dialog-leave-to {
    transition-duration: 300ms;
    transform: translateY(100vh);
  }

  .float-play-enter-active,
  .float-play-leave-active {
    transition-duration: 200ms;
    transform: translateY(0);
  }

  .float-play-enter-from,
  .float-play-leave-to {
    transition-duration: 200ms;
    transform: translateY(100%);
  }
}
</style>
