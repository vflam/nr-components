<template>
  <Teleport to="#popups" v-if="content">
    <div class="container" @click="stop">
      <div class="veil" @click="close_popup(true)"></div>
      <div class="box" :class="{ print }" :style="slotstyle">
        <div class="content" ref="content">
          <slot />
          <div class="close-wrap">
            <button :disabled="disabled" v-if="button" class="bouton close" @click="click_button">
              {{ button }}
            </button>
            <button v-if="print" class="bouton close" @click="do_print"> Print </button>
            <button v-if="!noclose" class="bouton close" @click="close_popup(true)">
              {{ text }}
            </button>
          </div>
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
      this.content = false;
      this.$emit("update:modelValue", this.content);
      this.$emit("close");
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
    do_print() {
      print();
    },
    before_print(e: Event) {
      const node = this.$refs.content as HTMLDivElement;
      let cloned = node.cloneNode(true);
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
  },
  watch: {
    modelValue(n) {
      this.content = n;
    },
    $route() {
      this.close_popup(false);
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
  overflow: auto;
  width: max-content;
  max-width: min(95%, 1200px);
  min-width: 300px;
}

.content {
  overflow: auto;
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
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
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
  }
}
.print {
  width: 100% !important;
  height: 100% !important;
  max-width: unset !important;
  max-height: unset !important;
  display: flex;
  .close-wrap {
    position: sticky;
    left: 50%;
    right: 50%;
    margin: unset;
    width: 250px;
    bottom: 20px;
    transform: translateX(-50%);
  }
  @media print {
    overflow: visible;
  }
  .content {
    margin-left: auto;
    margin-right: auto;
  }
}
</style>
