<template>
  <Form :model="userModel" :rules="rules" ref="loginFormRef">
    <FormItem label="用户名" prop="username" >
      <Input  v-model="userModel.username" placeholder="请输入用户名"  />
    </FormItem>
    <FormItem label="密码" prop="password">
      <Input  type="password" v-model="userModel.password"  />
    </FormItem>
  </Form>

  <button @click="login">登录</button>
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
    const loginFormRef = ref(null)
    const userModel = ref({
      username: '',
      password: ''
    })


    const validatePass = (rule, value, callback) => {
      if (!value) {
        callback(new Error('请输入密码'))
      }else if (!Number.isInteger(Number(value))) {
        callback(new Error('密码必须是纯数字'))
      }else {
        callback()
      }
    }
    const rules =  {
      username: [
        { required: true, message: '请输入用户名', trigger: 'blur' },
      ],
      password: [
        { required: true,  validator: validatePass, trigger: 'change' }
      ],
    }

    const login = () => {
      loginFormRef.value.validate(valid => {
        console.log(valid)
      })
    }

    return {
      inputContent,
      userModel,
      rules,
      loginFormRef,
      login
    }
  }
})
</script>

<style scoped>

</style>