<template>
  <div class="form-page-container">
  <Form :model="userModel" :rules="rules" ref="loginFormRef">
    <FormItem label="用户名" prop="username" >
      <Input  v-model="userModel.username" placeholder="请输入用户名"  />
    </FormItem>
    <FormItem label="密码" prop="password">
      <Input  type="password" v-model="userModel.password"  />
    </FormItem>
    <FormItem label="密码" prop="userType">
      <Select v-model="userModel.userType">
        <SelectOption value="user" label="user"> </SelectOption>
        <SelectOption value="test" label="test"> </SelectOption>
        <SelectOption value="vip">{{'这是VIP用户'}} </SelectOption>
        <SelectOption value="admin" label="admin" disabled> </SelectOption>
      </Select>
    </FormItem>
  </Form>
  <button @click="login">登录</button>
  </div>


</template>

<script>

import {defineComponent, ref} from "vue";
import Input from "../components/form/Input.vue";
import FormItem from '../components/form/FormItem.vue'
import Form from "../components/form/Form.vue";
import Select from '../components/select/Select.vue'
import SelectOption from '../components/select/Option.vue'
export default defineComponent({
  name: "FormPage",
  components:{
    Form,
    Input,
    FormItem,
    Select,
    SelectOption
  },
  setup() {
    const inputContent = ref('')
    const loginFormRef = ref(null)
    const userModel = ref({
      username: '',
      password: '',
      userType: 'user'
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
      userType: [
        { required: true,   message: '请选择用户类型', trigger: 'blur' }
      ],

    }

    const login = () => {
      loginFormRef.value.validate(valid => {
        console.log(valid)
        if (valid) {
          console.log(userModel.value)
        }
      })
    }

    return {
      inputContent,
      userModel,
      rules,
      loginFormRef,
      login,
    }
  }
})
</script>

<style scoped>
.form-page-container {
  margin: 0 auto;
  width: 400px;
}
</style>