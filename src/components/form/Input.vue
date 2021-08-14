<template>
  <input :type="type" :value="modelValue" @input="updateModelValue" v-bind="$attrs" />
</template>

<script>
import {defineComponent, inject, reactive, watch} from "vue";
import {FormItemKey, FormKey} from "./token";

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
    const formInstance = inject(FormKey)
    const formItemInstance = inject(FormItemKey)

    const updateModelValue = (e) => {
      context.emit('update:modelValue',e.target.value)
      // 如果有校验，通知父级去做校验
      formItemInstance.formItemMitt.emit('validate', e.target.value)
    }

    return {
      updateModelValue
    }
  }
})
</script>

<style scoped>

</style>