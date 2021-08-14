<template>
  <div>
<!--    label 展示-->
    <label v-if="label"> {{label}} </label>
    <slot></slot>
<!--    校验信息-->
    <p v-if="error" class="error-message"> {{error}}</p>
  </div>
</template>

<script>
import {defineComponent, inject, onMounted, provide, reactive, ref, toRefs} from "vue";
import {FormEvents, FormItemKey, FormKey} from "./token";
import mitt  from "mitt";
import Schema from 'async-validator'

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
    prop: {
      type:String,
      required: false
    }
  },
  setup(props, ctx) {
    const error = ref('')
    const formInstance = inject(FormKey)
    const formItemMitt = mitt()

    //执行校验
    const validate = () =>  {
      // 获取规则
      const rules = formInstance.rules[props.prop]
      //获取当前值
      const value = formInstance.model[props.prop]
    //  创建校验的描述对象
      const desc = {
        [props.prop]: rules
      }
    //  创建校验的实例
      const schema = new Schema(desc)
      schema.validate({
        [props.prop]: value
      }, errors => {
        if (errors) {
          error.value = errors[0].message
        }else {
        //  校验通过
          error.value = ''
        }
      })
    }


    onMounted(() => {
      if (props.prop) {
        formInstance.formMitt.emit(FormEvents.addField, elFormItemInstance)
      }
      formItemMitt.on('validate', () => {
        validate()
      })
    })

    const elFormItemInstance = reactive({
      ...toRefs(props),
      formItemMitt,
      validate,
    })

    provide(FormItemKey, elFormItemInstance)

    return {
      error,
      formInstance,
    }
  }
})
</script>

<style scoped>
.error-message {
  font-size: 12px;
  color: red;
}
</style>