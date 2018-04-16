<template>
  <a-box ref="box" :height="data.H" :width="data.W" :depth="10" :position="data.position" :color="data.color"></a-box>
</template>

<script>
export default {
  props: ['data'],
  mounted () {
    this.$refs.box.addEventListener('mouseover', () => {
      console.log('test')
    })
  }
}
</script>

<style lang="css">
</style>
