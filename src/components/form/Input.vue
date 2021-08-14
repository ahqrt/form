<template>
  <input :type="type" :value="modelValue" @input="updateModelValue" v-bind="$attrs" />
</template>

<script>
import {defineComponent, reactive, watch} from "vue";

export default defineComponent( {
  name: "Input",
  props:{
    modelValue: {
      type:String,
      default: ''
    },
    type :{
      type: String,
      default: 'text'
    }
  },
  inheritAttrs: false,
  setup(props, context) {
    const updateModelValue = (e) => {
      context.emit('update:modelValue',e.target.value)
    }

    return {
      updateModelValue
    }
  }
})
</script>

<style scoped>

</style>