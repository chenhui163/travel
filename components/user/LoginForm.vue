<template>
    <el-form 
        :model="form" 
        ref="form"
        :rules="rules" 
        class="form">

        <el-form-item class="form-item" prop="username">
            <el-input 
            v-model="form.username"
            placeholder="用户名/手机">
            </el-input>
        </el-form-item>

        <el-form-item class="form-item" prop="password">
            <el-input 
            v-model="form.password"
            placeholder="密码" 
            type="password">
            </el-input>
        </el-form-item>

        <p class="form-text">
            <nuxt-link to="#">忘记密码</nuxt-link>
        </p>

        <el-button 
        class="submit"
        type="primary"
        @click="handleLoginSubmit">
            登录
        </el-button>
    </el-form>

</template>

<script>
export default {

    // 数据
    data(){
        return {
            // 表单数据对象
            form:{
                username: "",
                password: ""
            },
            // 表单验证规则
            rules:{
                username: [
                    { required: true, message: '请输入用户名', trigger: 'blur' }
                ],
                password: [
                    { required: true, message: '请输入密码', trigger: 'blur' }
                ]
            }
        }
    },

    // 方法
    methods:{
        // 登录
        handleLoginSubmit(){
            // 验证表单数据是否完整
            this.$refs.form.validate( async (valid) => {
                if (valid) {
                    let res = await this.$axios({
                        url: "/accounts/login",
                        method: "POST",
                        data: this.form
                    });
                    const { status , data } = res;

                    if(status === 200 ) {
                        this.$message.success("登录成功！");

                        // 将数据存到store
                        // 通过调用mutation下的方法掉修改state的值
                        // commit方法调用mutation的etUserInfo方法进行修改,类似于$emit
                        this.$store.commit("user/setUserInfo", data);

                        // 清空表单数据对象
                        this.form = {
                            username: "",
                            password: ""
                        };
                        // 跳转到首页
                        this.$router.push("/");
                    }

                } else {
                    console.log('登录失败，请重新输入！');
                    this.form = {
                        username: "",
                        password: ""
                    };
                    return false;
                }
            });
        }
    }

}
</script>

<style scoped lang="less">
    .form{
        padding:25px;
    }

    .form-item{
        margin-bottom:20px;
    }

    .form-text{
        font-size:12px;
        color:#409EFF;
        text-align: right;
        line-height: 1;
    }

    .submit{
        width:100%;
        margin-top:10px;
    }
</style>