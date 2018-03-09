<template>
  
</template>
<script type="text/ecmascript-6">
const POSITIVE = 0
const NEGATIVE = 1
const ALL = 2
export default {
  props: {
      ratings: {
        type: Array,
        default (){
            return []
        }
      },
      selectType: {
        type: Number,
        default: ALL
      },
      onlyContent: {
        type: Boolean,
        default: false  
      },
      desc: {
        type: Object,
        default (){
            return []
        }
      }
  }
}
</script>
<style lang="scss" scoped>

</style>


