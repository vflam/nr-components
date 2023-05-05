<template>
  <span @click.stop="show = true">
    <button class="bouton">
      <slot name="button" />
    </button>
    <PopupDialog class="pop" v-if="show" @close="show = false">
      <h3>
        <slot name="button" />
      </h3>
      <slot name="content" />
    </PopupDialog>
  </span>
</template>

<script lang="ts">
import Vue from "vue";
import PopupDialog from "./PopupDialog.vue";

export default Vue.extend({
  data() {
    return {
      show: false,
    };
  },
  components: { PopupDialog },
});
</script>

<style scoped>
.pop .box {
  text-align: left !important;
}
</style>
