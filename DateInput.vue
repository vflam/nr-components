<template>
  <input v-model="dateString" type="date" @change="change" />
</template>
<script>
import { dateFormat } from "@/assets/shared/util.ts";

export default {
  props: ["value"],
  data() {
    return {
      dateString: null,
    };
  },

  created() {
    if (this.value == null) {
      this.dateString = dateFormat(new Date());
    } else {
      this.dateString = dateFormat(this.value);
    }
  },

  methods: {
    change() {
      this.$emit("input", new Date(this.dateString));
      this.$emit("change");
    },
  },
};
</script>
