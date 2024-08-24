<template>
  <Teleport to="#popups" v-if="content">
    <div class="container" @click="stop" :class="{ allowoverflow: overflow }">
      <div class="veil" @click="veil_close_popup"></div>
      <div class="box" :class="{ print }" :style="slotstyle">
        <div class="content" ref="content">
          <div class="head" v-if="x">
            <slot name="header" />
            <div @click="close" v-if="x" class="xCross imgBt">
              <img style="display: block" src="/assets/icons/blackcross.png" />
            </div>
          </div>

          <slot />
        </div>
        <div class="close-wrap">
          <slot name="boutons"></slot>
          <button :disabled="disabled" v-if="button" class="bouton close" @click="click_button">
            {{ button }}
          </button>
          <button v-if="!noclose" class="bouton close" @click="button_close_popup">
            {{ text }}
          </button>
        </div>
      </div>
    </div>
  </Teleport>
</template>

<script lang="ts">
import { getRandomKey } from "~/assets/shared/util";

export default {
  name: "PopupDialog",
  emits: ["button", "close", "update:modelValue", "cancel"],

  methods: {
    stop(event: any) {
      event.stopImmediatePropagation();
    },

    click_button() {
      this.$emit("button");
      if (this.buttonclose) {
        this.close();
      }
    },

    veil_close_popup() {
      if (this.nocloseonclickoutside) {
        return;
      }
      this.close();
    },

    button_close_popup() {
      this.close();
    },

    close() {
      if (this.mobile) {
        history.back();
      } else {
        this.doClose();
      }
    },

    doClose() {
      this.content = false;
      this.$emit("update:modelValue", this.content);
      this.$emit("cancel");
      this.$emit("close");
    },

    popstate(e: PopStateEvent) {
      if (!this.mobile) {
        this.doClose();
      } else {
        if (e.state.forward) {
          const parsed = e.state.forward.split("_pop");
          if (parsed.length > 1) {
            const uid = parsed[parsed.length - 1];
            if (uid === this.popupUid) {
              this.doClose();
            }
          }
        }
      }
    },

    esc_close_popup(e: KeyboardEvent) {
      e.stopPropagation();
      e.stopImmediatePropagation();
      if (e.key?.toLowerCase() === "escape") {
        this.close();
      }
    },

    before_print(e: Event) {
      const node = this.$refs.content as HTMLElement;
      let cloned = node.cloneNode(true) as HTMLElement;
      document.body.appendChild(cloned);
      cloned.classList.add("printable");
      const after_print = () => {
        document.body.removeChild(cloned);
      };
      addEventListener("afterprint", after_print, { once: true });
    },
    isMobile() {
      // @ts-ignore
      var match = window.matchMedia || window.msMatchMedia;
      if (match) {
        var mq = match("(pointer:coarse)");
        return mq.matches;
      }
      return false;
    },
  },

  mounted() {
    addEventListener("keydown", this.esc_close_popup);
    if (this.print) {
      addEventListener("beforeprint", this.before_print);
    }

    if (this.mobile) {
      this.$router.push({
        state: { type: "pop", id: this.popupUid },
        query: this.$route.query,
        hash: (window.location.hash || "#") + "_pop" + this.popupUid,
      });
    }
    addEventListener("popstate", this.popstate);
  },

  unmounted() {
    removeEventListener("keydown", this.esc_close_popup);
    if (this.print) {
      removeEventListener("beforeprint", this.before_print);
    }
    if (this.mobile) {
      removeEventListener("popstate", this.popstate);
    }
  },

  props: {
    modelValue: {
      default: true,
      type: Boolean,
    },
    text: {
      type: String,
      default: "Close",
    },
    noclose: {
      default: false,
      type: Boolean,
    },
    x: {
      default: false,
      type: Boolean,
    },
    button: {
      default: "",
      type: String,
    },
    disabled: {
      default: false,
      type: Boolean,
    },
    slotstyle: {
      default: "",
    },
    print: {
      type: Boolean,
      default: false,
    },
    buttonclose: {
      type: Boolean,
      default: true,
    },
    overflow: {
      type: Boolean,
      default: false,
    },
    keeponroutechange: {
      type: Boolean,
      default: false,
    },
    width: {
      type: String,
      default: "300px",
    },
    nocloseonclickoutside: {
      type: Boolean,
      default: false,
    },
  },
  watch: {
    modelValue(n) {
      this.content = n;
    },
  },
  data() {
    const key = getRandomKey();
    return {
      content: this.modelValue,
      mobile: this.isMobile(),
      popupUid: key,
    };
  },
};
</script>

<style scoped lang="scss">
@import "@/shared_components/css/vars.scss";

.container {
  z-index: 1000;
  position: fixed;
  top: 0px;
  left: 0px;
  right: 0px;
  bottom: 0px;
}

.box {
  background-color: $popups_background;
  background-image: $popups_background;
  z-index: 1001;
  display: inline-block;
  position: fixed;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  max-height: 80%;

  width: max-content;
  max-width: min(95%, 1200px);
  min-width: min(min(95%, 1200px), v-bind(width));

  overflow-y: auto;
  scrollbar-width: thin;
}
html.dark .box {
  scrollbar-color: rgba(255, 255, 255, 0.3) rgba(0, 0, 0, 0);
}
.close-wrap {
  margin-top: 10px !important;
}

.allowoverflow .content {
  overflow-y: visible;
}

.allowoverflow .box {
  overflow-y: visible;
}

.veil {
  position: fixed;
  top: 0px;
  left: 0px;
  right: 0px;
  bottom: 0;
  background-color: black;
  opacity: 0.7;
}

.box {
  padding: 5px;
}
.box.print {
  padding: unset !important;
  margin: unset !important;
  border: unset !important;
}

@media screen and (max-width: $large_mode) {
  .box {
    width: 95%;
    max-width: 95%;
  }
}

.close {
  display: inline-block;
  text-align: center;
}

.close-wrap {
  margin: auto;
  width: calc((100%) - 10px);
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  position: sticky;
  bottom: 0px;
}

.x {
  position: absolute;
  top: 4px;
  right: 4px;
  cursor: pointer;
}

@media print {
  .close-wrap {
    display: none;
  }

  .content {
    zoom: 70%;
    max-width: 1000px;
  }
}

.xCross {
  position: absolute;
  top: 6px;
  right: 4px;
  cursor: pointer;
}

.head {
  position: sticky;
  top: 0;
  background-color: rgba(0, 0, 0, 0.1);
  border: 1px solid $box_border;
  padding: 5px;
  margin-bottom: 5px;
  font-size: $fontHeaderSize;
  font-weight: bold;
  border-radius: $header_radius;
}

.print {
  width: 100% !important;
  height: 100% !important;
  max-width: unset !important;
  max-height: unset !important;
  display: flex;
  flex-direction: column;

  .close-wrap {
    position: sticky;
    margin-left: auto;
    margin-right: auto;
    width: 250px;
    bottom: 20px;
  }

  @media print {
    overflow: visible;
  }

  .content {
    margin-left: auto;
    margin-right: auto;
    // max-width: 1000px;
  }
}
</style>
