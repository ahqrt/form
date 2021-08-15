<template>
  <div class="el-form-item">
<!--    label 展示-->
    <label class="form-item__label" v-if="label" :style="`display: inline-block; width: ${typeof labelWidth === 'string'? labelWidth: labelWidth+ 'px'}` "> {{label}} </label>
    <slot></slot>
<!--    校验信息-->
  </div>
  <p v-if="error" class="error-message"> {{error}}</p>
</template>

<script>
import {defineComponent, inject, onMounted, provide, reactive, ref, toRefs} from "vue"
import {FormEvents, FormItemKey, FormKey} from "./token"
import mitt from "mitt"
import Schema from 'async-validator'

export default defineComponent( {
  name: "FormItem",
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
  setup(props) {
    const error = ref('')
    const formInstance = inject(FormKey)
    const formItemMitt = mitt()
    const validateMessage = ref('')
    const NOOP = () => {};
    const getRules = () => {
      const formRules = formInstance.rules[props.prop]
      return [].concat(formRules)
    }

    // 校验blur事件
    const onFieldBlur = () => {
      validate('blur')
    }

    const onFieldChange = () => {
      validate('change')

    }

    // 获取当前rules的校验类型
    const getFilteredRule = trigger => {
      const rules = getRules()
      return rules
          .filter(rule => {
            if (!rule.trigger || trigger === '') return true
            if (Array.isArray(rule.trigger)) {
              return rule.trigger.indexOf(trigger) > -1
            } else {
              return rule.trigger === trigger
            }
          })
          .map(rule => ({ ...rule }))
    }


    const addValidateEvents = () => {
      const rules = getRules()
      if (rules && rules.length > 0) {
        formItemMitt.on('form.blur', onFieldBlur)
        formItemMitt.on('form.change', onFieldChange)
      }
    }

    //执行校验
    const validate = (trigger, callback = NOOP) =>  {
      // 获取规则
      const rules = getFilteredRule(trigger)
      if (!rules || rules.length === 0) {
        callback()
        return
      }

      if (rules && rules.length >0) {
        rules.forEach(rule => {
          delete rule.trigger
        })
      }
      //  获取当前值
      const value = formInstance.model[props.prop]
      //  创建校验的描述对象
      const desc = {
        [props.prop]: rules
      }
      //  创建校验的实例
      const schema = new Schema(desc)

      return schema.validate({
        [props.prop]: value
      }, {firstFields: true}, (errors, invalidField) => {
        validateMessage.value = errors ? errors[0].message :''
        callback(validateMessage.value, invalidField)
        if (errors) {
          console.log('errors', errors)
          error.value = errors[0].message
        } else {
          error.value = ''
        }
      })
    }


    onMounted(() => {
      if (props.prop) {
        formInstance.formMitt.emit(FormEvents.addField, elFormItemInstance)
      }
      addValidateEvents()
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

<style>
.error-message {
  color: #f56c6c;
  font-size: 12px;
  line-height: 1;
  padding-top: 4px;
  top: 100%;
  left: 0;
}
.form-item__label {
  flex: 0 0 auto;
  text-align: right;
  font-size: 14px;
  color: #606266;
  line-height: 40px;
  padding: 0 12px 0 0;
  box-sizing: border-box;
  cursor: default;
}
.el-form-item {
  display: flex;
  margin-bottom: 22px;
}
</style>