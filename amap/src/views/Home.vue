<template>
  <div class="home">
    <a-map></a-map>
  </div>
</template>

<script>
import AMap from "@/components/map.vue";

export default {
  name: "home",
  components: {
    AMap
  }
};
</script>

<style lang="scss" scoped>
.home {
  position: absolute;
  top: 0;
  bottom: 0;
  height: 100%;
  width: 100%;
}
</style>

