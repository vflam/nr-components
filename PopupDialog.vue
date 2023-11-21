<template>
  <Teleport to="#popups" v-if="content">
    <div class="container" @click="stop" :class="{ allowoverflow: overflow }">
      <div class="veil" @click="veil_close_popup"></div>
      <div class="box" :class="{ print }" :style="slotstyle">
        <div class="content" ref="content">
          <slot />
        </div>
        <div class="close-wrap">
          <slot name="boutons"></slot>
          <button :disabled="disabled" v-if="button" class="bouton close" @click="click_button">
            {{ button }}
          </button>
          <button v-if="!noclose" class="bouton close" @click="close_popup(true)">
            {{ text }}
          </button>
        </div>
      </div>
    </div>
  </Teleport>
</template>

<script lang="ts">
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
        this.content = false;
        this.$emit("update:modelValue", this.content);
        this.$emit("close");
      }
    },
    veil_close_popup() {
      if (this.nocloseonclickoutside) {
        return;
      }
      this.close_popup(true);
    },
    close_popup(emit = true) {
      this.content = false;
      this.$emit("update:modelValue", this.content);
      if (emit) {
        this.$emit("cancel");
        this.$emit("close");
      }
    },
    esc_close_popup(e: KeyboardEvent) {
      if (e.key?.toLowerCase() === "escape") {
        if (this.content) {
          this.close_popup();
        }
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
  },
  mounted() {
    addEventListener("keydown", this.esc_close_popup);
    if (this.print) {
      addEventListener("beforeprint", this.before_print);
    }
  },
  unmounted() {
    removeEventListener("keydown", this.esc_close_popup);
    if (this.print) {
      removeEventListener("beforeprint", this.before_print);
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
    $route() {
      if (!this.keeponroutechange) {
        this.close_popup(false);
      }
    },
  },
  data() {
    return {
      content: this.modelValue,
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
  background-image: linear-gradient(
      rgba(var(--bg-r), var(--bg-g), var(--bg-b), var(--bg-a)),
      rgba(var(--bg-r), var(--bg-g), var(--bg-b), var(--bg-a))
    ),
    var(--bg-texture);
  background: var(--bg-size);
  background-color: rgb(var(--bg-r), var(--bg-g), var(--bg-b));
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
  scrollbar-color: rgba(0, 0, 0, 0.3) rgba(0, 0, 0, 0);
}

.content {
  padding-bottom: 10px;
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

@media screen and (max-width: $large_mode) {
  .box {
    width: 95%;
    max-width: 95%;
  }
}

.close {
  margin-top: 10px;
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
