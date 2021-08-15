<template>
  <div class="el-input">
    <input :type="type" :value="modelValue"
           @input="updateModelValue"
           @blur="handleBlur"
           class="el-input__inner"
           v-bind="$attrs" />
  </div>
</template>

<script>
import {defineComponent, inject, watch} from "vue";
import {FormItemKey} from "./token";

export default defineComponent( {
  name: "Input",
  props:{
    modelValue: {
      type:[String, Number],
      default: ''
    },
    type :{
      type: String,
      default: 'text'
    },
    validateEvent: {
      type: Boolean,
      default: true,
    },
  },
  inheritAttrs: false,
  setup(props, context) {
    const formItemInstance = inject(FormItemKey, {})

    const handleBlur = event => {
      if (props.validateEvent) {
        formItemInstance?.formItemMitt?.emit('form.blur', [props.modelValue])
      }
    }

    const updateModelValue = (e) => {
      context.emit('update:modelValue',e.target.value)
    }

    watch(() => props.modelValue, newVal => {
      if (props.validateEvent) {
        formItemInstance?.formItemMitt?.emit('form.change', [newVal])
      }
    })

    return {
      updateModelValue,
      handleBlur
    }
  }
})
</script>

<style lang="less">
.el-input {
  --el-input-font-color: #606266;
  --el-input-border: 1px;
  --el-input-border-color: #dcdfe6;
  --el-input-border-radius: 4px;
  --el-input-background-color: #fff;
  --el-input-placeholder-color: #c0c4cc;
  --el-input-hover-border: #c0c4cc;
  --el-input-clear-hover-color: #909399;
  --el-input-focus-border: #409eff;
  --el-border-width-base: 1px;
  --el-text-color-regular: #606266;
  position: relative;
  font-size: 14px;
  display: inline-block;
  width: 100%;
  line-height: 40px;
  .el-input__inner {
    -webkit-appearance: none;
    background-color: var(--el-input-background-color);
    background-image: none;
    border-radius: var(--el-input-border-radius);
    border: var(--el-input-border);
    box-sizing: border-box;
    color: var(--el-input-font-color);
    display: inline-block;
    font-size: inherit;
    height: 40px;
    line-height: 40px;
    outline: none;
    padding: 0 15px;
    width: 100%;
    border-color: var(--el-input-border-color);
    border-style: solid;
    &:hover {
      border-color: var(--el-input-hover-border)
    }
    &:focus {
      outline: none;
      border-color: var(--el-input-focus-border)

    }
  }
}
</style>