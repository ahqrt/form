<template>
  <div class="el-form">
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

      let promise

      // 如果不是函数，进行处理
      if (typeof cb !== 'function') {
        promise = new Promise((resolve , reject) => {
          cb = function (valid, invalidField) {
            if (valid) {
              resolve(true)
            }else {
              reject(invalidField)
            }
          }
        })
      }

      if (fields.length === 0) {
        cb(true)
      }


      let valid = true
      let count = 0
      let invalidFields = {}

      for (const field of fields) {
        field.validate('', (message, field) => {
          if (message) {
            valid = false
          }
          invalidFields = {...invalidFields, ...field}
          if (++count === fields.length) {
            cb(valid, invalidFields)
          }
        })
      }
      return promise
    }


    provide(FormKey, formInstance)

    return {
      validate
    }
  }
}
</script>

<style lang="less">
.el-form {
  font-size: 14px;
}
</style>