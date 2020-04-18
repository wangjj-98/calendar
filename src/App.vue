<template>
  <div id="app">
    <date-picker v-model="now"></date-picker>
  </div>
</template>
  <script>
import DatePicker from './component/DatePicker'
export default {
  data () {
    return {
      now: new Date(),
    }
  },
  components: {
    DatePicker
  }
}
  </script>
<style lang="less">
</style>
