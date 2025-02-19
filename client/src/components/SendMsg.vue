<template>
  <div class="send-msg-container" @click.prevent.stop>
    <msgList :msgList="msgList" :show="showMsgList"></msgList>
    <div class="send-msg-body">
      <div class="msg-name iconfont icon-msg" @click="showMsgList = !showMsgList"></div>
      <div class="msg-name quick-send" @click="showPresets = !showPresets"></div>
      <div class="msg-input">
        <input type="text" @keyup.13="send" v-model="msg" @focus="onMsgInputFocus" />
        <ul class="presets" :class="{ show: showPresets }" ref="presetsContainer">
          <li v-for="(item, index) in presets" :key="index">
            <div class="preset-content" @click="sendPreset(item)">{{ item }}</div>
            <button class="btn-remove-preset" @click="removePreset(index)">×</button>
          </li>
          <li class="custom-preset">
            <input v-model="customPreset" @keyup.13="addPreset" />
            <button class="btn-add-preset" @click="addPreset">+</button>
          </li>
        </ul>
        <div class="msg-btn btn" @click="send"><span>send</span></div>
      </div>
      <voice-recorder @audio="sendAudio"></voice-recorder>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Watch, Vue } from 'vue-property-decorator';
import msgList from '@/components/msgList.vue';
import VoiceRecorder from '@/components/VoiceRecorder.vue';

const PRESET_STORE_KEY = 'msg-presets';

@Component({
  components: {
    msgList,
    VoiceRecorder,
  },
})
export default class SendMsg extends Vue {
  public msg: string = '';
  public showMsgList: boolean = false;
  public showPresets: boolean = false;
  public customPreset: string = '';
  public presets: string[] = [];
  @Prop({ type: Number, default: 1000 }) public max!: number;
  @Prop() public msgList!: string[];

  public mounted() {
    this.presets = this.getPresets();
    document.addEventListener('click', this.documentClickHandler);
  }

  public beforeDestroy() {
    document.removeEventListener('click', this.documentClickHandler);
  }

  public getPresets() {
    let items = JSON.parse(localStorage.getItem(PRESET_STORE_KEY) || 'null');

    if (!items) {
      items = [
        '生意淡薄，不如赌搏',
        '搏一搏，单车变摩托',
        '又再搏多搏，摩托变饭壳',
        '多谢老细!',
        '666',
        'All佢啦!',
        '跟佢啦!',
        '咩啊?',
        '燶嗮!',
        '燶唔燶?',
        '扑街啦',
      ];

      this.savePresets(items);
    }

    return items;
  }

  public send() {
    if (this.msg !== '') {
      this.$emit('send', this.msg);
      this.msg = '';
      this.showPresets = false;
    }
  }

  public sendAudio(audioData: any) {
    this.$emit('sendAudio', audioData);
  }

  public sendPreset(msg: string) {
    this.$emit('send', msg);
    this.showPresets = false;
  }

  public onMsgInputFocus() {
    this.showPresets = false;
    this.scrollPresetsToBottom();
  }

  public addPreset() {
    if (this.customPreset) {
      this.presets = [...this.presets, this.customPreset];
      this.savePresets();
      this.customPreset = '';
      this.$nextTick(() => {
        this.scrollPresetsToBottom();
      });
    }
  }

  public removePreset(index: number) {
    this.confirm({
      message: '是否删除?',
      callback: (yes) => {
        if (yes) {
          this.presets = this.presets.filter((_, idx) => idx !== index);
          this.savePresets();
        }
      },
    });
  }

  public savePresets(presets?: string[]) {
    localStorage.setItem(PRESET_STORE_KEY, JSON.stringify(presets || this.presets));
  }

  public scrollPresetsToBottom() {
    const presetsContainer = this.$refs.presetsContainer as HTMLUListElement;
    presetsContainer.scrollTo(0, presetsContainer.scrollHeight);
  }

  public documentClickHandler() {
    this.showPresets = false;
  }

  public confirm({ message, callback }: { message: string; callback: (confirm: boolean) => void }) {
    const modal = document.createElement('div');
    Object.assign(modal.style, {
      position: 'fixed',
      left: '50%',
      top: '50%',
      minWidth: '200px',
      transform: 'translate(-50%, -50%)',
      zIndex: 9999,
      background: 'white',
      borderRadius: '4px',
      fontSize: '14px',
    });
    modal.innerHTML = `
      <div style="text-align: center; padding: 12px;">${message}</div>
      <div style="text-align: right; padding: 12px;">
        <a href="javascript:void(0)" data-action='cancel' style="text-decoration: none; color: #777">取消</a>
        <a href="javascript:void(0)" data-action='confirm' style="text-decoration: none; color: #00976e; margin-left: 2em">确认</a>
      </div>
    `;
    modal.addEventListener('click', (e) => {
      e.preventDefault();
      e.stopPropagation();
      const target = e.target as HTMLDivElement;
      if (target?.matches(`[data-action='cancel']`)) {
        modal.remove();
        callback(false);
      } else if (target?.matches(`[data-action='confirm']`)) {
        modal.remove();
        callback(true);
      }
    });
    document.body.append(modal);
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
.send-msg-container {
  position: fixed;
  z-index: 1;
  .send-msg-body {
    position: fixed;
    width: 100vw;
    height: 25px;
    padding: 10px 0;
    left: 0;
    bottom: 0;
    background-color: #fff;
    font-size: 12px;
    display: flex;
    align-items: center;

    .msg-name {
      flex: none;
      width: 40px;
      font-size: 30px;
      color: #009870;
      text-align: center;
    }

    .quick-send {
      background: url('../assets/icon/quick-send.svg') center no-repeat;
      height: 28px;
    }

    .msg-input {
      position: relative;
      flex: 1;
      text-align: left;
      display: flex;
      align-items: center;

      input {
        height: 30px;
        flex: 1;
        padding: 0 5px;
        font-size: 12px;
        border-radius: 4px;
        background-color: rgba(0, 0, 0, 0.05);
      }

      .msg-btn {
        flex: none;
        width: 80px;
        margin-top: 0;
        margin-left: 0;
        text-align: center;

        span {
          padding: 5px 10px;
        }
      }
    }

    .presets {
      position: absolute;
      bottom: calc(100% + 1px);
      left: 0;
      width: 600px;
      max-width: 100%;
      max-height: 80vh;
      margin: 0;
      border-radius: 4px;
      font-size: 16px;
      list-style: none;
      z-index: 2;
      background-color: rgba(0, 0, 0, 0.65);
      color: white;
      overflow: auto;
      opacity: 0;
      visibility: hidden;
      transition: all 0.2s;

      &.show {
        opacity: 1;
        visibility: visible;
      }

      li {
        display: flex;
        align-items: center;
        color: white;
        padding: 6px 12px;
        cursor: pointer;
      }

      .preset-content {
        flex: 1;
        cursor: pointer;
      }

      input {
        flex: 1;
        color: white;
        background-color: rgba(255, 255, 255, 0.35);
        border: none;
        border-radius: 4px;
        height: 28px;
      }

      button {
        background: none;
        border: none;
        flex: none;
        padding: 0 8px 0 16px;
        color: white;
        height: 28px;
        font-size: 24px;

        &.btn-remove-preset {
          color: red;
        }
      }
    }
  }

  /deep/ .voice-indicator {
    left: unset;
    right: 0;
    transform: none;
  }
}
</style>
