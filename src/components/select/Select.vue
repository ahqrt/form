<template>
  <transition name="fade">
    <div
        :class="['select']"
        tabindex="0"
        @click.stop="isOpen = !isOpen"
        @blur="handleBlur"
        @focus="handleFocus"
    >
      <div>
        <template v-if="!selectItems.length">
          <span class="placeholder">{{ placeholder }}</span>
        </template>

        <template v-else>
          <div>{{ selectItems[0].label }}</div>
        </template>
      </div>

      <div class="select-dropdown" v-show="isOpen">
        <slot></slot>
      </div>
    </div>
  </transition>
</template>

<script>
import {computed, inject, provide, reactive, ref, toRefs, watch} from "vue";
import mitt from "mitt"
import {selectKey, updateSelectValueKey} from "./token";
import {FormItemKey} from "../form/token";

export default {
  props: {
    placeholder: { type: String, default: "请选择" },
    optionKey: { type: String, default: "value" },
    modelValue: { type: [String, Object, Number, Array] },
    validateEvent: {
      type: Boolean,
      default: true,
    },
  },
  setup(props, ctx) {
    const isOpen = ref(false)
    const selectValue = ref(props.modelValue || [])
    const selectItems = ref([])
    const  restValueNum = computed(() => selectItems.value.length - 1)
    const selectMitt = mitt()
    const formItemInstance = inject(FormItemKey)
    const handleBlur = (event) => {
      isOpen.value = false;
      if (props.validateEvent) {
        formItemInstance.formItemMitt?.emit('form.blur', [props.modelValue])
      }
      ctx.emit("blur", event);
    }

    const handleFocus = (event) => {
      ctx.emit("focus", event);
    }

    selectMitt.on(updateSelectValueKey, (value) => {
      if (value) {
        selectValue.value = value
      }
    })


    const selectInstance = reactive({
      ...toRefs(props),
      selectMitt,
      ctx,
      selectValue,
      isOpen,
      selectItems
    })

    provide(selectKey, selectInstance)


    watch(() => props.modelValue, (newVal) => {
      selectValue.value = newVal
      if (props.validateEvent) {
        formItemInstance.formItemMitt?.emit('form.change', [newVal])
      }
    }, {immediate: true})

    watch(() => selectValue.value, (value)=> {
      ctx.emit('update:modelValue', value)
      selectItems.value = []
    })

    return {
      isOpen,
      selectValue,
      selectItems,
      restValueNum,
      handleBlur,
      handleFocus
    }
  },

  // model: {
  //   prop: "value",
  //   event: "input"
  // },
  // data() {
  //   return {
  //     isOpen: false,
  //     selectValue: [],
  //     selectItems: []
  //   };
  // },
  // provide() {
  //   return {
  //     fatSelect: this
  //   };
  // },
  // computed: {
  //   restValueNum() {
  //     return this.selectItems.length - 1;
  //   }
  // },
  // watch: {
  //   value: {
  //     handler(value) {
  //       const { multiple } = this;
  //       const init = value ? value : multiple ? [] : "";
  //       this.selectValue = multiple ? [...init] : init;
  //     },
  //     immediate: true
  //   },
  //   selectValue: {
  //     handler(value) {
  //       this.selectItems = [];
  //     }
  //   }
  // },
  // methods: {
  //   handleDelete(item) {
  //     const { value } = item;
  //     this.selectValue = this.selectValue.filter(item => item !== value);
  //     this.$emit("input", this.selectValue);
  //     this.$emit("change", this.selectValue);
  //   },
  // }
};
</script>

<style lang="less">
.select {
  position: relative;
  padding: 0 24px 0 12px;
  width: 100%;
  min-height: 32px;
  line-height: 32px;
  box-sizing: border-box;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 14px;
  color: rgba(0, 0, 0, 0.65);
  text-overflow: ellipsis;
  outline: none;
  transition: all 0.2s;
  &:focus {
    text-overflow: ellipsis;
    border: 1px solid #209cee;
    box-shadow: 0 0 5px 0 rgba(32, 156, 238, 0.5);
  }
  &.is-disabled {
    background-color: rgba(192, 196, 204, 0.25);
    color: #c0c4cc;
    cursor: not-allowed;
    &:focus {
      box-shadow: none;
      border: 1px solid #ccc;
    }
  }
  .placeholder {
    color: #999;
  }
  .select__item__tag {
    position: relative;
    display: inline-flex;
    align-items: center;
    margin: 2px 6px 2px 0;
    padding: 4px 4px;
    height: 1em;
    color: #909399;
    background: #f0f2f5;
    border-radius: 4px;
    .delete-btn {
      margin-left: 4px;
      border-radius: 50%;
      color: #fff;
      background: #c0c4cc;
      cursor: pointer;
    }
  }
  .arrow {
    position: absolute;
    top: 100%;
    left: 0;
    right: 0;
    margin: auto auto;
    display: inline-block;
    align-items: center;
    width: 14px;
    height: 14px;
  }
  .select-dropdown {
    position: absolute;
    top: 100%;
    left: 0;
    padding: 4px 0;
    box-sizing: border-box;
    min-width: 100%;
    max-height: 170px;
    overflow: auto;
    z-index: 2;
    background: #fff;
    border: 1px solid #ccc;
    border-radius: 0 0 4px 4px;
  }
}
</style>