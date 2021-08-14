<template>
  <Form :model="userModel" :rules="rules">
    <FormItem label="用户名">
      <Input v-model="userModel.username" placeholder="请输入用户名"  />
    </FormItem>
    <FormItem label="密码">
      <Input type="password" v-model="userModel.password"  />
    </FormItem>
  </Form>
  <h2>{{inputContent}} </h2>
</template>

<script>

import {defineComponent, ref} from "vue";
import Input from "../components/form/Input.vue";
import FormItem from '../components/form/FormItem.vue'
import Form from "../components/form/Form.vue";
export default defineComponent({
  name: "FormPage",
  components:{
    Form,
    Input,
    FormItem
  },
  setup() {
    const inputContent = ref('')
    const userModel = ref({
      username: '',
      password: ''
    })
    const rules =  {
      username: [
        { required: true, message: '请输入用户名', trigger: 'blur' },
      ],
      password: [
        { required: true, message: '请输入密码', trigger: 'change' }
      ],
    }
    return {
      inputContent,
      userModel,
      rules
    }
  }
})
</script>

<style scoped>

</style>