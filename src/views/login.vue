<style lang="less">
    @import './login.less';
</style>

<template>
    <div class="login" @keydown.enter="handleSubmit">
        <div class="login-con">
            <Card :bordered="false">
                <p slot="title" class="title">
                    <!-- <Icon type="log-in"></Icon> -->
                    后台管理系统
                </p>
                <div class="cert">
                    <img src="./aiwrap.png" alt="">
                </div>
                <div class="form-con">
                    <Form ref="loginForm" :model="form" :rules="rules">
                        <FormItem prop="userName">
                            <Input v-model="form.userName" placeholder="用户名">
                                <span slot="prepend">
                                    <Icon :size="16" type="person"></Icon>
                                </span>
                            </Input>
                        </FormItem>
                        <FormItem prop="password">
                            <Input type="password" v-model="form.password" placeholder="密码">
                                <span slot="prepend">
                                    <Icon :size="14" type="locked"></Icon>
                                </span>
                            </Input>
                        </FormItem>
                        <FormItem>
                            <Button @click="handleSubmit" long>登录</Button>
                        </FormItem>
                    </Form>
                </div>
            </Card>
        </div>
    </div>
</template>

<script>
import Cookies from 'js-cookie';
import util from '@/libs/util';
import {ERR_OK} from '@/config/config'
export default {
    data () {
        return {
            form: {
                userName: 'iview_admin',
                password: ''
            },
            rules: {
                userName: [
                    { required: true, message: '账号不能为空', trigger: 'blur' }
                ],
                password: [
                    { required: true, message: '密码不能为空', trigger: 'blur' }
                ]
            }
        };
    },
    methods: { 
        handleSubmit () {
            let one_hour = 1/24;
            this.$refs.loginForm.validate((valid) => {
                if (valid) {
                    let jsonData = new URLSearchParams();
                    jsonData.append('username', this.form.userName);
                    jsonData.append('password', this.form.password);
                    Cookies.set('user', this.form.userName, {expires: one_hour});
                    util.title("后台管理系统");
                    this.$router.push({
                        name: 'home_index'
                    });
                    // util.ajax.post('/login',jsonData).then((response)=>{
                    //   if(response.data.status == ERR_OK){
                    //     Cookies.set('user', this.form.userName, {expires: one_hour});
                    //     // Cookies.set('password', this.form.password, {expires: one_hour});
                    //     Cookies.set('token', response.data.token, {expires: one_hour});
                    //     // this.$store.commit('setToken', response.data.data)
                    //     this.$store.commit('setAvator', 'https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=3448484253,3685836170&fm=27&gp=0.jpg');
                    //     if (this.form.userName === 'iview_admin') {
                    //         Cookies.set('access', 0);
                    //     } else {
                    //         Cookies.set('access', 1);
                    //     }
                    //     // this.$router.push({
                    //     //     name: 'resourceData'
                    //     // });
                    //     window.location.reload();
                    //   }else {
                    //     this.$Message.error(response.data.message);
                    //   }
                    // })
                }
            });
        }
    },
    created () {
        util.title("用户登陆");
    }
};
</script>

<style>

</style>
