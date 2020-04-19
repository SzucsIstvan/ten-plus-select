<template>
  <ten-plus-select v-model="select" />
</template>

<script>
import TenPlusSelect from "./TenPlusSelect";

export default {
  components: { TenPlusSelect },
  data() {
    return {
      select: 100
    };
  }
};
</script>