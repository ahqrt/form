<template>
  <div>
    <slot></slot>
  </div>
</template>

<script>
import {getCurrentInstance, provide, reactive, ref, toRefs} from "vue";
import {FormEvents, FormKey} from "./token";
import mitt from 'mitt'

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
  emits: ['validate'],
  setup(props) {
    const formMitt = mitt()
    const formInstance = reactive({
      formMitt,
      ...toRefs(props)
    })

    // 所有的formItem
    const fields = []



    formMitt.on(FormEvents.addField, field => {
      if (field) {
        fields.push(field)
      }
    })

    formMitt.on(FormEvents.removeField, field => {
      if (field.prop) {
        fields.splice(fields.indexOf(field), 1)
      }
    })

    // 全局校验规则
    const validate = ( cb ) => {
      if (!props.model) {
        console.warn( '[Form]model is required for validate to work!')
        return
      }
      // 遍历所有formItem，执行，获取结果
      const tasks = fields.filter(item => item.prop).map(item => item.validate())
    //  统一处理所有的promise 结果
      console.log(tasks)
      Promise.all(tasks).then(() => cb(true)).catch(() => cb(false))
    }


    provide(FormKey, formInstance)

    return {
      validate
    }
  }
}
</script>

<style scoped>

</style>