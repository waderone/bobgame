<template>
  <ThePage color="#f2f2f2">
    <template slot="headerContent">
      <div class="head-name">
        <div class="name">傻子</div>
        <div class="status">当前在线</div>
        <div class="info-card" @click="$router.push('/friends/friendinfomation/10')"><i class="iconfont icon-wode"></i></div>
        <div class="info-bar">
          <img :src="friendInfo.headimg">
          <div class="line-one">{{friendInfo.name}}</div>
          <div class="line-two">{{`${friendInfo.sex?'女':'男'}·${friendInfo.star}·${friendInfo.city}`}}</div>
          <div class="btn-addfriend"><i class="iconfont icon-add"></i>加好友</div>
        </div>
      </div>
    </template>
    <template slot="content">
      <div class="chat-container" :class="[recordOpen?'open-record':'',faceOpen?'open-face':'',gameOpen?'open-game':'']">
        <div class="record-block">
          <transition name="fade-in">
            <Progress :percentage="circleValue" class="vue-progress" v-if="currentTime" :pivot-text="circleValue | toString" />
          </transition>
          <div class="tip-text" :style="{color:recordBtnColor}">
            {{tipText}}
          </div>
          <div class="btn-record" @touchstart="startMic" @touchend="stopMic" @touchmove="stopCancel">
            <i class="iconfont icon-yuyin" :style="{backgroundColor:recordBtnColor}"></i>
          </div>
        </div>
        <ChatFace @emitFace="addFace" @deleteFace="deleteFace"></ChatFace>
        <div class="game-block">
          <swiper :options="gameOption" ref="mySwiper" class="face-container">
            <swiperSlide v-for="i in 5" :key="i">
              <div class="game-slide">
                <div class="swiper-game-item">
                  <img src="http://bobgame.cn/Uploads/Picture/2018-09-12/5b98b0612b4e9.png">
                  疯狂兔子
                </div>
                <div class="swiper-game-item">
                  <img src="http://bobgame.cn/Uploads/Picture/2018-09-12/5b98b0612b4e9.png">
                  疯狂兔子
                </div>
                <div class="swiper-game-item">
                  <img src="http://bobgame.cn/Uploads/Picture/2018-09-12/5b98b0612b4e9.png">
                  疯狂兔子
                </div>
                <div class="swiper-game-item">
                  <img src="http://bobgame.cn/Uploads/Picture/2018-09-12/5b98b0612b4e9.png">
                  疯狂兔子
                </div>
                <div class="swiper-game-item">
                  <img src="http://bobgame.cn/Uploads/Picture/2018-09-12/5b98b0612b4e9.png">
                  疯狂兔子
                </div>
              </div>
            </swiperSlide>
            <div class="game-pagination" slot="pagination"></div>
          </swiper>
        </div>
        <div class="chat-input">
          <div class="quick-msg">
            <div class="item">这把完了加好友！</div>
            <div class="item">来啊，决战到天亮！</div>
            <div class="item">连麦么，我萝莉音</div>
            <div class="item">老铁666啊</div>
            <div class="item">就算是神我也要杀给你看！</div>
            <div class="item">蒙多说你是个大娘们儿</div>
          </div>
          <div class="icon icon-01" @click="openSlide('record')"><i class="iconfont icon-yuyinzuo"></i></div>
          <textarea v-model="chatInput" @focus="chatFocus" @blur="chatBlur" :class="[chatClass]" @keypress.enter="emitMessage" ref="emitInput"></textarea>
          <div class="icon icon-02" @click="openSlide('game')"><i class="iconfont icon-gamepad"></i></div>
          <div class="icon icon-03" @click="openSlide('face')"><i class="iconfont icon-xiaolian"></i></div>
        </div>
        <!-- <div class="friend-info">
          <div class="btn-add"></div>
        </div> -->
        <div class="chat-main has-topbar" ref="chatBlock" @click="closeAllSlide">
          <div class="chat-list">
            <div class="chat-line center">
              <div class="center-main">2018-09-16 20:32</div>
            </div>
            <div class="chat-line">
              <div class="battle-info-block">
                <div class="battle-head-block">
                  <img :src="friendInfo.headimg">
                  <img src="http://bobgame.cn/Uploads/Picture/2018-06-30/5b374f918199d.png">
                </div>
                <div class="top-block">
                  <h3>一起聊天玩游戏吧~</h3>
                  <h4>我们都爱玩的游戏</h4>
                </div>
                <div class="bottom-block">
                  <div class="game-item">
                    <img src="http://bobgame.cn/Uploads/Picture/2018-09-12/5b98b0612b4e9.png">
                  </div>
                  <div class="game-item">
                    <img src="http://bobgame.cn/Uploads/Picture/2018-08-27/5b84002384301.jpg">
                  </div>
                  <div class="game-item">
                    <img src="http://bobgame.cn/Uploads/Picture/2018-08-22/5b7d267a247a5.png">
                  </div>
                </div>
              </div>
            </div>
            <div class="chat-line center">
              <div class="center-main">临时对话保留24小时，成为好友永久保留对话。</div>
            </div>


            <div class="chat-line" v-for="(item,index) in chatData" :key="index">
              <div :class="[`chat-item-${item.isMe?'right':'left'}`]">
                <div class="head">
                  <img :src="item.isMe?headUrl:friendInfo.headimg" alt="">
                </div>

                <div class="text-content" v-if="item.type === 'text'" v-html="encodeFace(item.content)">

                </div>

                <ChatVoiceItem class="audio-content" v-else-if="item.type === 'voice'" v-bind="item"></ChatVoiceItem>
              </div>
            </div>
          </div>

          <!-- <div class="chat-line">
            <div class="chat-item-left">
              <div class="head">
                <img :src="friendInfo.headimg" alt="">
              </div>
              <div class="game-content">
                <div class="game-title"></div>
                <div class="btn-status"></div>
              </div>
            </div>
          </div> -->
        </div>
        <!-- 好友页面
          <button class="btn-mic" @click="startMic">开始录音</button>
          <button class="btn-mic" @click="stopMic">停止录音</button>
          <div class="audio">
            <audio :src="audio" @canplay="playing"></audio>
            <span v-if="audio">{{recordTime}}</span>
          </div> -->
      </div>
    </template>
  </ThePage>
</template>

<script>
  import uploader from "../../unit/oss.js";
  import moment from "moment";
  import { Progress } from "vant";
  import ChatVoiceItem from "@/components/ChatVoiceItem";
  import ChatFace from "@/components/ChatFace";
  export default {
    components: { Progress, ChatVoiceItem, ChatFace },
    data() {
      return {
        luyin: null,
        audio: {},
        long: 0,
        chatInput: "点击输入内容",
        scrollValue: 0,
        recordOpen: false,
        faceOpen: false,
        gameOpen: false,
        currentRate: 0,
        currentTime: 0,
        currentPlayer: null,
        recordBtnColor: '#ccc',
        tipText: '长按开始录音',
        cursorStart: null,
        cursorEnd: null,
        friendInfo: {
          headimg: 'http://bobgame.cn/Uploads/Picture/2018-08-13/5b711405a3680.jpg',
          name: '大宝贝儿',
          sex: 0,
          star: '金牛座',
          city: '浙江杭州'
        },
        touchStartY: 0,
        cancel: false,
        chatData: [{
          type: 'text',
          content: '哈喽~'
        }, {
          type: 'text',
          content: '《守望先锋》官微宣布中国队在2018《守望先锋》世界杯泰国曼谷站的小组赛中，以小组第一的战绩顺利晋级八强，并将与另外七支晋级队伍一同登上2018暴雪嘉年华的舞台，争夺最终的冠军。'
        }, {
          type: 'text',
          content: '哈喽~',
          isMe: true
        }, {
          type: 'text',
          content: '哈喽~'
        },
        {
          type: 'voice',
          fileName: '12c73da07edcd8d8_3515.mp3',
          isPlayDone: false
        },
        {
          type: 'voice',
          fileName: '1536986007545.amr',
          isMe: true,
          isLocal: true,
          time: 20000
        }, {
          type: 'text',
          content: '《守望先锋》官微宣布中国队在2018《守望先锋》世界杯泰国曼谷站的小组赛中，以小组第一的战绩顺利晋级八强，并将与另外七支晋级队伍一同登上2018暴雪嘉年华的舞台，争夺最终的冠军。',
          isMe: true
        }],
        gameOption: {
          loop: false,
          spaceBetween: 10,
          pagination: {
            el: '.game-pagination',
            clickable: true,
            bulletClass: 'game-item',
            bulletActiveClass: 'game-item-active'
          }
        }
      };
    },
    computed: {
      chatClass() {
        return this.chatInput === "点击输入内容" ? "" : "not-empty";
      },
      circleValue() {
        if (this.currentTime === 0) {
          return false
        }
        return parseInt((this.currentTime / 1000 / 60) * 100)
      },
      userInfo() {
        return this.$store.state.userInfo;
      },
      headUrl() {
        let url = this.$store.state.userInfo.head_icon;
        if (/http/g.test(url)) {
          return url;
        } else {
          return "http://cdn.bobgame.cn" + url;
        }
      }
    },
    // filters:{
    //   encodeFace(val){
    //     let face_text = val;
    // 		let zz = /\[em_(0|[1-9]\d?)\]/g;
    // 		let zz_num = /(0|[1-9]\d?)/g;
    // 		if(face_text.length){
    // 			let fac = face_text.match(zz);
    // 			for(let i in fac){
    // 				let fac_num = fac[i].match(zz_num);
    // 				face_text = face_text.replace("[em_"+fac_num+"]","<img src='/img/face/" + fac_num + ".png'/>");
    // 			}
    //     }
    //     return face_text
    //   }
    // },
    mounted() {
      this.scrollBottom();
    },
    methods: {
      openSlide(type) {
        this.closeAllSlide();
        this[`${type}Open`] = true;
        this.scrollBottom();
      },
      closeAllSlide() {
        let that = this;
        let arr = ["record", "face", "game"];
        arr.map(item => {
          that[`${item}Open`] = false;
        });
      },
      setPaddingTop(value) {
        console.log(value);
      },
      startMic($event) {
        let that = this;
        let startTime = moment();
        that.recordBtnColor = '#82ed6d'
        that.tipText = '松开发送，上滑取消'
        that.currentTime = moment().diff(startTime)
        that.touchStartY = $event.changedTouches[0].screenY

        window.recordTimer = setInterval(() => {
          that.currentTime = moment().diff(startTime)
        }, 200)

        that.$atApp(() => {
          let r = (that.luyin = window.plus.audio.getRecorder());
          r.record({ filename: "_doc/audio/", format: "amr" },
            function (r) {
              //判断是否取消
              if (!that.cancel) {
                let allTime = moment().diff(startTime);
                uploader(r, allTime)
                  .then(res => {
                    res;
                    that.chatData.push({
                      type: 'voice',
                      fileName: r.split('/')[2],
                      isMe: true,
                      isLocal: true,
                      time: allTime
                    })
                    that.scrollBottom();
                  })
                  .catch(err => {
                    console.log(err);
                  });
              } else {
                that.cancel = false
              }
            },
            function (e) {
              alert("Audio record failed: " + e.message);
            }
          );
          // window.plus.uploader.enumerate( arr=>{
          //   console.log(JSON.stringify(arr));
          // });
        });
      },
      stopCancel($event) {
        let that = this
        if ($event.changedTouches[0].screenY <= that.touchStartY - 100) {
          that.recordBtnColor = '#ff3535'
          that.tipText = '松开取消发送语音'
          that.cancel = true
        } else {
          that.recordBtnColor = '#82ed6d'
          that.tipText = '松开发送，上滑取消'
          that.cancel = false
        }
      },
      stopMic() {
        let r = this.luyin;
        window.clearInterval(window.recordTimer);
        // alert(JSON.stringify(r))
        this.recordBtnColor = '#ccc'
        this.tipText = '长按开始录音'
        this.currentTime = 0
        this.$atApp(() => {
          r.stop();
        })
      },
      chatFocus() {
        let that = this;
        if (this.chatInput === "点击输入内容") {
          this.chatInput = "";
        }
        that.scrollBottom();
        that.closeAllSlide();
      },
      chatBlur() {
        this.cursorStart = this.$refs.emitInput.selectionStart
        this.cursorEnd = this.$refs.emitInput.selectionEnd
        console.log(this.$refs.emitInput.selectionStart);
        if (this.chatInput === "") {
          this.chatInput = "点击输入内容";
        }
      },
      scrollBottom() {
        let that = this;
        setTimeout(() => {
          that.$refs.chatBlock.scrollTop = 9999999999;
        }, 200);
      },
      emitMessage($event) {
        $event.preventDefault()
        let that = this
        that.chatData.push({
          type: 'text',
          content: that.chatInput,
          isMe: true
        })
        that.scrollBottom();
        that.chatInput = ''
      },
      addFace(val) {
        let that = this
        if (this.chatInput === '点击输入内容') {
          that.chatInput = val
          that.cursorStart = that.cursorEnd = that.cursorEnd + val.length;
        } else {
          let text = that.chatInput
          let resText = text.substr(0, that.cursorStart) + val + text.substr(that.cursorEnd, text.length);
          this.chatInput = resText;
          that.cursorStart = that.cursorEnd = that.cursorEnd + val.length;
        }


        // this.$refs.emitInput.focus();
      },
      deleteFace() {
        let that = this
        let text = that.chatInput;
        // 获取光标之前的字符串
        let changedText = text.substr(0, that.cursorStart);
        let len = changedText.length;
        let reg = /\[em_([0-9]*)\]$/g;
        // 删除表情code块或最后一个字符
        if (reg.test(changedText)) {
          changedText = changedText.replace(reg, "");
        } else {
          changedText = changedText.substring(0, changedText.length - 1);
        }
        let resText = changedText + text.substr(that.cursorEnd, text.length);
        that.chatInput = resText
        // 调整光标位置
        that.cursorStart = that.cursorEnd = that.cursorEnd - (len - changedText.length);
      },
      encodeFace(val) {
        let face_text = val;
        let zz = /\[em_(0|[1-9]\d?)\]/g;
        let zz_num = /(0|[1-9]\d?)/g;
        if (face_text.length) {
          let fac = face_text.match(zz);
          for (let i in fac) {
            let fac_num = fac[i].match(zz_num);
            face_text = face_text.replace("[em_" + fac_num + "]", "<img class='content-face' src='/img/face/" + fac_num + ".png'/>");
          }
        }
        return face_text
      }
    },
    beforeDestroy() {
      let that = this
      that.$atApp(() => {
        if (that.currentPlayer) {
          that.currentPlayer.stop();
          that.currentPlayer = null
        }
      })
    }
  };
</script>

<style lang="scss">
  button {
    width: 200px;
    height: 100px;
  }

  .head-name {
    padding-top: 17px;

    .name {
      font-size: 28px;
      line-height: 28px;
      color: #333;
    }

    .status {
      padding-top: 8px;
      font-size: 18px;
      line-height: 18px;
      color: #999;
    }
  }

  .info-bar {
    height: 88px;
    background: #fff;
    position: absolute;
    bottom: -88px;
    width: 100%;
    padding-top: 15px;
    padding-left: 100px;
    text-align: left;
    line-height: 30px;

    .line-one {
      font-size: 24px;
    }

    .line-two {
      font-size: 18px;
      color: #adadad;
    }

    img {
      width: 60px;
      height: 60px;
      border-radius: 50%;
      display: block;
      position: absolute;
      left: 25px;
      top: 12px;
    }

    .btn-addfriend {
      position: absolute;
      right: 25px;
      padding: 0 15px;
      height: 50px;
      background: #fa3a50;
      color: #fff;
      top: 19px;
      border-radius: 25px;
      line-height: 52px;
      font-size: 20px;
      @include tapColor;

      .iconfont {
        font-size: 20px;
        padding-right: 5px;
      }
    }
  }

  .chat-container {
    background: #f5f5f5;
    width: 100%;
    height: 100%;
    position: relative;
    overflow: hidden;

    .chat-input {
      height: 100px;
      width: 100%;
      position: absolute;
      bottom: 0;
      left: 0;
      right: 0;
      background: #fff;
      padding: 14px 180px 14px 100px;
      transition: bottom 0.2s;

      textarea {
        display: block;
        width: 100%;
        height: 100%;
        border-radius: 10px;
        background: #f5f5f5;
        border: 0;
        resize: none;
        font-size: 28px;
        color: #ccc;
        padding: 16px 30px;
        line-height: 40px;

        &.not-empty {
          color: #333;
        }
      }

      .icon {
        position: absolute;
        width: 100px;
        height: 100px;

        bottom: 0;

        .iconfont {
          display: block;
          position: absolute;
          top: 50%;
          left: 50%;
          transform: translate(-50%, -50%);
          width: 56px;
          height: 56px;
          border-radius: 50%;
          text-align: center;
          line-height: 58px;
          font-weight: bold;
          border: 2px solid #666;
          color: #666;
        }

        &.icon-01 {
          left: 0;
        }

        &.icon-02 {
          padding-left: 10px;
          right: 90px;
          width: 80px;

          .iconfont {
            background: #fbcb38;
            vertical-align: 3px;
            border-color: #fbcb38;
            color: #fff;
            font-size: 36px;
            line-height: 52px;
          }
        }

        &.icon-03 {
          padding-right: 10px;
          right: 0;
          width: 90px;
        }
      }
    }

    .chat-main {
      position: absolute;
      top: 0;
      left: 0;
      right: 0;
      bottom: 100px;
      // background: #f5f5f5;
      overflow-y: auto;
      -webkit-overflow-scrolling: touch;
      transition: bottom 0.2s;

      .chat-list {
        position: relative;
        padding-top: 40px;
        padding-bottom: 90px;
      }

      &.has-topbar {
        .chat-list {
          padding-top: 128px;
        }
      }
    }

    .quick-msg {
      position: absolute;
      top: -90px;
      left: 0;
      right: 0;
      height: 90px;
      z-index: 100;
      white-space: nowrap;
      overflow-y: auto;
      padding-right: 15px;

      .item {
        display: inline-block;
        padding: 0 24px;
        font-size: 24px;
        line-height: 72px;
        height: 70px;
        border-radius: 35px;
        background: #fff;
        margin-left: 15px;
        box-shadow: 0 4px 20px rgba(0, 0, 0, .05);
        @include tapMask;
        overflow: hidden;
      }
    }

    .record-block {
      height: 270px;
      width: 100%;
      position: absolute;
      bottom: 0;
      left: 0;
      right: 0;
      z-index: 10;
      transform: translateY(100%);
      transition: transform 0.2s;
      -webkit-touch-callout: none;
      -webkit-user-select: none;
      user-select: none;
    }



    .game-block {
      height: 260px;
      width: 100%;
      position: absolute;
      bottom: 0;
      left: 0;
      right: 0;
      z-index: 10;
      transform: translateY(100%);
      transition: transform 0.2s;
    }

    &.open-record {
      .record-block {
        transform: translateY(0);
      }

      .chat-main {
        bottom: 370px;
      }

      .chat-input {
        bottom: 270px;
      }
    }

    &.open-face {
      .face-block {
        transform: translateY(0);
      }

      .chat-main {
        bottom: 370px;
      }

      .chat-input {
        bottom: 270px;
      }
    }

    &.open-game {
      .game-block {
        transform: translateY(0);
      }

      .chat-main {
        bottom: 360px;
      }

      .chat-input {
        bottom: 260px;
      }
    }
  }

  .vue-progress {
    width: 100%;
    z-index: 20;

    .van-progress__pivot {
      display: none !important;
    }
  }

  .btn-record {
    width: 150px;
    height: 150px;
    position: absolute;
    top: 80px;
    left: 50%;
    transform: translateX(-50%);
    color: #fff;
    z-index: 10;

    .iconfont {
      width: 146px;
      height: 146px;
      display: block;
      font-size: 96px;
      border-radius: 50%;
      text-align: center;
      line-height: 156px;
      position: absolute;
      top: 2px;
      left: 2px;
      transition: all .2s;
    }
  }

  .tip-text {
    position: absolute;
    top: 30px;
    font-size: 24px;
    color: #333;
    text-align: center;
    width: 100%;
  }

  .chat-line {
    width: 100%;
    margin-bottom: 30px;

    &.center {
      height: 40px;
      text-align: center;
    }

    .center-main {
      display: inline-block;
      padding: 0 12px;
      background: #d8dbdf;
      color: #fff;
      height: 40px;
      border-radius: 5px;
      font-size: 18px;
      line-height: 44px;
    }
  }

  .battle-info-block {
    width: 500px;
    margin: 0 auto;
    border-radius: 10px;
    overflow: hidden;
    padding-top: 70px;
    position: relative;
    margin-bottom: 30px;

    .battle-head-block {
      width: 100%;
      height: 140px;
      text-align: center;
      position: absolute;
      top: 0;
      left: 0;
      right: 0;

      >img {
        display: inline-block;
        width: 140px;
        height: 140px;
        object-fit: cover;
        border-radius: 50%;

        &:first-child {
          margin-right: 40px;
        }
      }
    }

    .top-block {
      height: 200px;
      width: 100%;
      background-image: linear-gradient(120deg, #89f7fe 0%, #66a6ff 100%);
      border-radius: 10px 10px 0 0;
      color: #fff;
      text-align: center;
      padding-top: 100px;

      h3 {
        font-size: 30px;
        padding: 0;
        margin: 0;
        line-height: 30px;
        padding-bottom: 20px;
      }

      h4 {
        font-size: 24px;
        line-height: 24px;
        opacity: .5;
        padding: 0;
        margin: 0;
      }
    }

    .bottom-block {
      width: 100%;
      height: 175px;
      padding: 20px 25px 25px 25px;
      background: #fff;

      .game-item {
        width: 130px;
        height: 130px;
        display: inline-block;
        margin-left: 30px;

        &:first-child {
          margin-left: 0;
        }

        img {
          width: 100%;
          height: 100%;
          object-fit: cover;
        }
      }
    }
  }

  .chat-item-left {
    padding-left: 120px;
    position: relative;
    @include clearfix;
    min-height: 80px;

    .head {
      position: absolute;
      left: 25px;
      top: 0;
      border-radius: 50%;
      overflow: hidden;

      img {
        display: block;
        width: 80px;
        height: 80px;
      }
    }

    .text-content {
      padding: 20px 30px;
      background: #fff;
      max-width: 500px;
      float: left;
      font-size: 28px;
      line-height: 40px;
      min-height: 80px;
      border-radius: 10px;
    }

  }

  .chat-item-right {
    padding-right: 120px;
    position: relative;
    @include clearfix;
    min-height: 80px;

    .head {
      position: absolute;
      right: 25px;
      top: 0;
      border-radius: 50%;
      overflow: hidden;

      img {
        display: block;
        width: 80px;
        height: 80px;
      }
    }

    .text-content {
      padding: 20px 30px;
      background: #fff;
      max-width: 500px;
      float: right;
      font-size: 28px;
      line-height: 40px;
      min-height: 80px;
      border-radius: 10px;
    }
  }

  .content-face {
    display: inline-block;
    width: 40px;
    height: 40px;
    vertical-align: -9px;
  }

  .info-card {
    right: 10px;
    top: 0;
    width: 88px;
    height: 88px;
    position: absolute;

    .iconfont {
      font-size: 48px;
      color: #666;
    }
  }

  .game-slide {
    padding: 0 45px;
    padding-top: 40px;

    .swiper-game-item {
      width: 100px;
      display: inline-block;
      padding-bottom: 20px;
      margin-right: 40px;
      font-size: 22px;
      text-align: center;
      line-height: 56px;

      &:last-child {
        margin-right: 0;
      }

      img {
        width: 100px;
        height: 100px;
        object-fit: cover;
        display: block;
        border-radius: 15px;
      }
    }
  }


  .game-pagination {
    text-align: center;
    position: absolute;
    bottom: 10px !important;

    .game-item {
      margin-left: 12px;
      display: inline-block;
      width: 16px;
      height: 16px;
      border-radius: 50%;
      background: #c2c2c2;

      &.game-item-active {
        background: #999;
      }
    }
  }
</style>