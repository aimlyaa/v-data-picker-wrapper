<template>
  <v-app>
    <v-main>
      <VDatePickerWrapper set-date="22/03/2015" format="DD/MM/YYYY"></VDatePickerWrapper>
    </v-main>
  </v-app>
</template>

<script>
import VDatePickerWrapper from './components/VDatePickerWrapper';

export default {
  name: 'App',

  components: {
    VDatePickerWrapper,
  },

  data: () => ({
    //
  }),
};
</script>
