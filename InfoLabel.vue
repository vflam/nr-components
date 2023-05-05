<template>
  <span>
    <label :for="forstring" :title="title">
      <slot name="label"></slot>
    </label>

    <InfoButton v-if="$slots.info">
      <h3>
        <slot name="label" />
      </h3>
      <slot name="info" />
    </InfoButton>
  </span>
</template>

<script>
import InfoButton from "./InfoButton.vue";

export default {
  props: {
    for: String,
    title: String,
  },
  components: { InfoButton },
  computed: {
    forstring() {
      return this.for;
    },
  },
};
</script>

<style scoped>
h3 {
  text-align: left;
  margin-bottom: 5px;
  background-color: rgba(0, 0, 0, 0.1);
  font-size: 18px;
  padding: 2px;
  padding-left: 5px;
}
</style>
