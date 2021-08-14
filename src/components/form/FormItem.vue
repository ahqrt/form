<template>
  <div>
<!--    label 展示-->
    <label v-if="label"> {{label}} </label>
    <slot></slot>
<!--    校验信息-->
    <p v-if="error"> {{error}}</p>
    <h3>{{formInstance}}</h3>
  </div>
</template>

<script>
import {defineComponent, inject, ref} from "vue";
import {FormKey} from "./token";

export default defineComponent( {
  name: "form-item",
  props:{
    label: {
      type:String,
      default: ''
    },
    labelWidth: {
      type: [String, Number],
      default: ''
    },
    prop: String
  },
  setup() {
    const error = ref('')
    const formInstance = inject(FormKey)
    console.log('formInstance', formInstance)
    return {
      error,
      formInstance
    }
  }
})
</script>

<style scoped>

</style>