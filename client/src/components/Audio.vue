<template>
  <div class="audio-container">
    <audio ref="click" controls>
      <source src="../assets/mp3/click.mp3" type="audio/mpeg" />
      Your browser does not support the audio element.
    </audio>
    <audio ref="raise" controls>
      <source src="../assets/mp3/raise.mp3" type="audio/mpeg" />
      Your browser does not support the audio element.
    </audio>
    <audio ref="raise-notice" controls>
      <source src="../assets/mp3/raise-notice.mp3" type="audio/mpeg" />
      Your browser does not support the audio element.
    </audio>
    <audio ref="fold" controls>
      <source src="../assets/mp3/fold.mp3" type="audio/mpeg" />
      Your browser does not support the audio element.
    </audio>
    <audio ref="income" controls>
      <source src="../assets/mp3/income.mp3" type="audio/mpeg" />
      Your browser does not support the audio element.
    </audio>
    <audio ref="allin-notice" controls>
      <source src="../assets/mp3/allin-notice.mp3" type="audio/mpeg" />
      Your browser does not support the audio element.
    </audio>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';

@Component
export default class Audio extends Vue {
  @Prop() public type!: string;
  @Prop() public play!: boolean;
  @Watch('play')
  public wPlay(val: boolean) {
    if (val) {
      (this.$refs[this.type] as HTMLAudioElement).play();
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
.audio-container {
  position: absolute;
  z-index: -99;
  display: none;
}
</style>
