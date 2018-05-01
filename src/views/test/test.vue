<template>
  <div class="test">
    {{msg}}
  </div>
</template>

<script>
  export default {
    data () {
      return {
        msg: '测试一下哦'
      }
    }
  }
</script>

<style lang="less">
  .test{
    color: red
  }
</style>