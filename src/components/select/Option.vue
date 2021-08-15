<template>
  <div
      :class="[
      'select-option',
      { 'is-selected': isSelect },
      { 'is-disabled': disabled }
    ]"
      @click.stop="handleClick"
  >
    <template v-if="label">
      <span> {{label}} </span>
    </template>
    <template v-else>
      <slot></slot>
    </template>
  </div>
</template>

<script>
import {computed, inject, watch} from "vue";
import {selectKey, updateSelectValueKey} from "./token";

export default {
  props: {
    value: { type: [Object, String, Number], required: true },
    label: { type: String },
    disabled: { type: Boolean, default: false }
  },

  setup(props) {
    const selectInstance = inject(selectKey)
    const isSelect  = computed(() => {
      const {optionKey, selectItems} = selectInstance
      return selectItems.find(item => item.key === optionKey)
    })

    const  handleSelect =()  => {
      selectInstance.selectMitt.emit(updateSelectValueKey, props.value)
      selectInstance.isOpen = false
      selectInstance.ctx.emit('change', selectInstance.selectValue)
      selectInstance.ctx.emit('update:modelValue', selectInstance.selectValue)
    }

    const handleClick = ()  => {
        handleSelect();
    }

    watch(() => selectInstance.selectValue, (newValue) => {
      const { optionKey } = selectInstance
      const {value, label } = props
      const key = optionKey
      if (
          newValue === value ||
          (Array.isArray(newValue) && newValue.find(item => item === value))
      ) {
        selectInstance.selectItems = [
          {
            key,
            label,
            value
          }
        ];
      }
    }, {immediate: true})
    return {
      isSelect,
      handleSelect,
      handleClick
    }
  },
};
</script>

<style lang="less">
.select-option {
  padding: 0 24px 0 12px;
  cursor: pointer;
  &:not(.is-disabled):hover {
    background-color: #f5f7fa;
  }
  &.is-selected {
    color: #209cee;
  }
  &.is-disabled {
    color: #c0c4cc;
    cursor: not-allowed;
  }
}
</style>
