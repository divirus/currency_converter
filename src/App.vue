<template>
  <CalcForm></CalcForm>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import CalcForm from '@/components/CalcForm.vue';

@Component({
  components: {
    CalcForm,
  },
})
export default class App extends Vue {}
</script>

<style>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }

  body {
    padding: 20px;
    background: #e1e6f2;
  }
</style>
