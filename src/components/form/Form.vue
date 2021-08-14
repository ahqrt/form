<template>
  <div>
    <slot></slot>
  </div>
</template>

<script>
import {provide, reactive, toRefs} from "vue";
import {FormKey} from "./token";

export default {
  name: "Form",
  props:{
    model: {
      type:Object,
      required: true

    },
    rules: {
      type: Object,
      required: false
    }
  },
  setup(props) {
    const formInstance = reactive({
      ...toRefs(props)
    })

    provide(FormKey, formInstance)
  }
}
</script>

<style scoped>

</style>