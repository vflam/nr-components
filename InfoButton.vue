<template>
  <span @click.stop="show = true">
    <img class="imgBt" src="/assets/icons/i.png" />
    <PopupDialog v-if="show" @close="show = false">
      <slot />
    </PopupDialog>
  </span>
</template>

<script lang="ts">
import Vue from "vue";
import PopupDialog from "./PopupDialog.vue";

export default Vue.extend({
  data() {
    return {
      show: false
    }
  },
  components: { PopupDialog }
})
</script>

<style scoped>
img {
  display: inline-block !important;
  vertical-align: -2px;
}
</style>