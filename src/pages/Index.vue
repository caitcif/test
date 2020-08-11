<template>
  <q-page class="flex flex-center">
    <Authentication />
  </q-page>
</template>

<script>
import Authentication from "components/Authentication";
export default {
  name: "PageIndex",
  components: { Authentication }
};
</script>
