<template>
  <span @click.stop="show = true">
    <img class="imgBt" src="/assets/icons/i.png" />
    <PopupDialog v-if="show" @close="show = false">
      <slot />
    </PopupDialog>
  </span>
</template>

<script>
import PopupDialog from "./PopupDialog.vue";

export default {
  data() {
    return {
      show: false,
    };
  },
  components: { PopupDialog },
};
</script>

<style scoped>
img {
  display: inline-block !important;
  vertical-align: -2px;
}
</style>
