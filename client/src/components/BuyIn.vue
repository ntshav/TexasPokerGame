<template>
  <Transition name="fade">
    <div class="buy-in" v-show="showBuyIn">
      <div class="shadow" @click="closeBuyIn"></div>
      <div class="buy-in-body">
        <div class="input-bd">
          <div class="input-name">
            <span>buy in:&nbsp;</span>
            <input type="number" v-model="value" disabled />
          </div>
        </div>
        <div class="btn"><span @click="buyIn">buy in</span></div>
      </div>
    </div>
  </Transition>
</template>

<script lang="ts">
import { Component, Prop, Watch, Vue } from 'vue-property-decorator';
import range from './Range.vue';

@Component({
  components: {
    range,
  },
})
export default class BuyIn extends Vue {
  @Prop() public showBuyIn!: boolean;
  @Prop() public value!: any;

  public closeBuyIn() {
    this.$emit('update:showBuyIn', false);
  }

  public async buyIn() {
    this.closeBuyIn();
    this.$emit('buyIn', Number(this.value));
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
.buy-in {
  position: fixed;
  z-index: 99;

  .shadow {
    position: fixed;
    z-index: 9;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.2);
  }

  .buy-in-body {
    z-index: 99;
    position: fixed;
    left: 50%;
    top: 50%;
    margin: -100px -150px;
    width: 300px;
    border-radius: 12px;
    box-sizing: border-box;
    background: #fff;
    padding: 20px;
  }

  .input-text {
    input {
      width: 100px;
    }
  }
  .input-name {
    margin-bottom: 15px;
    font-size: 20px;
    text-align: center;
    input {
      width: 70px;
      font-size: 20px;
    }
  }
  .btn {
    margin-top: 20px;
  }
}
</style>
