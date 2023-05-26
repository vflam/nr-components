<template>
  <span @click.stop="show = true">
    <button class="bouton">
      <slot name="button" />
    </button>
    <PopupDialog v-if="show" @close="show = false">
      <h3>
        <slot name="button" />
      </h3>
      <slot name="content" />
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
.pop .box {
  text-align: left !important;
}
</style>
