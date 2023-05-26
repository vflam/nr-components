<template>
  <input v-model="dateString" type="date" @change="change" />
</template>
<script>
import { dateFormat } from "@/assets/shared/util.ts";

export default {
  props: ["value", "modelValue"],
  data() {
    return {
      dateString: null,
    };
  },

  created() {
    let val = this.modelValue;
    if (val == null) {
      this.dateString = dateFormat(new Date());
    } else {
      this.dateString = dateFormat(this.modelValue);
    }
  },

  methods: {
    change() {
      this.$emit("update:modelValue", new Date(this.dateString));
      this.$emit("change");
    },
  },
};
</script>
