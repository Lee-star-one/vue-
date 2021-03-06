<template>
  <div class="gulu-toast" :class="toastClasses">
    <div class="toast" ref="wrapper">
    <div class="message">
      <div v-if="enableHtml" v-html="$slots.default[0]"></div>
      <slot v-else></slot>
    </div>
    <div class="line" ref="line"></div>
    <span class="close" v-if="closeButton" @click="onClickClose">
      {{ closeButton.text }}
    </span>
  </div>
  </div>
</template>
<script>
export default {
  name: "GuluToast",
  props: {
    autoClose: {
      type: [Boolean,Number],
      default: 3,
      validator(value){
        return value === false || typeof value === "number"
      }
    },
    closeButton: {
      type: Object,
      default() {
        return {
          text: "关闭",
          callback: undefined,
        };
      },
    },
    enableHtml: {
      type: Boolean,
      default: false,
    },
    position:{
      type:String,
      default:'top',
      validator(value){
        return ['top','bottom','middle'].includes(value)
      }
    }
  },
  computed:{
    toastClasses(){
      return {
        [`position-${this.position}`]:true
      }
    }
  },
  mounted() {
    this.execAutoClose();
    this.updateStyle()
  },
  methods: {
    updateStyle() {
      this.$nextTick(() => {
        this.$refs.line.style.height =
          this.$refs.wrapper.getBoundingClientRect().height + "px";
      });
    },
    execAutoClose() {
      if (this.autoClose) {
        setTimeout(() => {
          this.close();
        }, this.autoClose * 1000);
      }
    },
    close() {
      this.$el.remove();
      this.$destroy();
    },
    log() {
      console.log("tost内部方法");
    },
    onClickClose() {
      this.close();
      if (this.closeButton && typeof this.closeButton.callback === "function") {
        this.closeButton.callback(this);
      }
    },
  },
};
</script>
<style lang="less" scoped>
@font-size: 14px;
@toast-min-height: 40px;
@toast-bg: rgba(0,0,0,0.75);
@keyframes slide-up {
0% {opacity: 0; transform: translateY(100%);}
100% {opacity: 1;transform: translateY(0%);}
}
@keyframes slide-down {
0% {opacity: 0; transform: translateY(-100%);}
100% {opacity: 1;transform: translateY(0%);}
}
@keyframes fade-in {
0% {opacity: 0; }
100% {opacity: 1;}
}
.gulu-toast {
  position: fixed;
  left: 50%;
  transform: translateX(-50%);
  @animation-duration: 300ms;
  &.position-top {
    top: 0;
    .toast {
      border-top-left-radius: 0;
      border-top-right-radius: 0;
      animation: slide-down @animation-duration;
    }
  }
  &.position-bottom {
    bottom: 0;
    .toast {
      border-bottom-left-radius: 0;
      border-bottom-right-radius: 0;
      animation: slide-up @animation-duration;
    }
  }
  &.position-middle {
    top: 50%;
    transform: translateX(-50%) translateY(-50%);
    .toast {
      animation: fade-in @animation-duration;
    }
  }
}
.toast {
  font-size: @font-size;
  max-width: 300px;
  min-height: @toast-min-height;
  line-height: 1.8;
  display: flex;
  align-items: center;
  background: @toast-bg;
  border-radius: 4px;
  box-shadow: 0 0 3px 0 rgba(0, 0, 0, 0.5);
  padding: 0 16px;
  .message{
    padding: 8px 0;
  }
  .line {
    border-left: 1px solid #666;
    height: 100%;
    margin-left: 16px;
  }
  .close {
    padding-left: 16px;
    flex-shrink: 0;
  }
}
</style>