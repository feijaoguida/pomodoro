<template>
  <v-dialog v-model="dialog" persistent max-width="600px">
    <v-card> Meu dialog </v-card>
  </v-dialog>
</template>

<script>
export default {
  props: {
    dialog: {
      type: Boolean,
      require: true,
    },
  },
};
</script>