<template>
  <div class="slide-bar-container">
    <!--    <div class="value">{{raiseSize}}</div>-->
    <div class="range-body">
      <input type="range" v-model="rangeSize" :class="{ horizontal: !!isHorizontal }" />
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';

@Component
export default class Range extends Vue {
  @Prop({ type: Number, default: 1000 }) public max!: number;
  @Prop({ type: Number, default: 100 }) public min!: number;
  @Prop() public value!: any;
  @Prop({ type: Boolean, default: false }) public isHorizontal!: boolean;
  public rangeRound = (this.max - this.min) / 100;

  get rangeSize() {
    const valNum = Number(this.value);
    const size =
      valNum >= this.max ? this.max / this.rangeRound : valNum < this.min ? 0 : (valNum - this.min) / this.rangeRound;
    return size;
  }

  set rangeSize(val) {
    const valNum = Number(val);
    const size = valNum === 0 ? this.min : Math.floor((valNum / 100) * (this.max - this.min)) + this.min;
    this.$emit('change', size);
    this.$emit('input', size);
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
.slide-bar-container {
  .range-body {
    line-height: 10px;
  }

  .value {
    margin-bottom: 10px;
  }

  input[type='range'] {
    -webkit-appearance: none;
    width: 200px;
    border-radius: 10px; /*这个属性设置使填充进度条时的图形为圆角*/
    vertical-align: middle;
    display: inline-block;

    &.horizontal {
      transform: rotateZ(-90deg) translate3d(-50%, 0, 0);
      transform-origin: center;
      margin-left: -8px;
    }
  }

  input[type='range']:focus {
    outline: none;
  }

  input[type='range']::-webkit-slider-thumb {
    -webkit-appearance: none;
    height: 30px;
    width: 30px;
    margin-top: -12px; /*使滑块超出轨道部分的偏移量相等*/
    background: #ffffff;
    border-radius: 50%; /*外观设置为圆形*/
    border: solid 0.125em rgba(205, 224, 230, 0.5); /*设置边框*/
    box-shadow: 0 0.125em 0.125em #3b4547; /*添加底部阴影*/
  }

  input[type='range']::-webkit-slider-runnable-track {
    height: 6px;
    border-radius: 10px; /*将轨道设为圆角的*/
    box-shadow: 0 1px 1px #def3f8, inset 0 0.125em 0.125em #0d1112; /*轨道内置阴影效果*/
  }
}
</style>
