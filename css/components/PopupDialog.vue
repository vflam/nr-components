<template>
  <Teleport to="#popups" v-if="content">
    <div class="container" @click="stop">
      <div class="veil" @click="close_popup"></div>
      <div class="box" :style="slotstyle">
        <img
          v-if="x"
          @click="close_popup"
          class="x"
          src="/assets/icons/redcross.png"
        />
        <div class="content">
          <slot />
          <div class="close-wrap">
            <button v-if="button" class="bouton close" @click="$emit('button')">
              {{ button }}
            </button>
            <button v-if="!noclose" class="bouton close" @click="close_popup">
              {{ text }}
            </button>
          </div>
        </div>
      </div>
    </div>
  </Teleport>
</template>
<script>
import Teleport from "vue2-teleport";
export default {
  name: "PopupDialog",
  components: { Teleport },

  methods: {
    stop(event) {
      event.stopImmediatePropagation();
    },
    close_popup(emit = true) {
      this.content = false;
      this.$emit("input", this.content);
      if (emit) {
        this.$emit("cancel");
        this.$emit("close");
      }
    },
  },
  props: {
    value: {
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
    slotstyle: {
      default: "",
    },
  },
  watch: {
    value(n) {
      this.content = n;
    },
    $route() {
      this.close_popup(false);
    },
  },
  data() {
    return {
      content: this.value,
    };
  },
};
</script>

<style scoped lang="scss">
@import "@/assets/css/vars.scss";

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
    url(/assets/images/no.jpg);
  background-size: var(--bg-size);
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
  text-align: center;
}

.x {
  position: absolute;
  top: 4px;
  right: 4px;
  cursor: pointer;
}
</style>
