<template>
	<el-form :model="user" status-icon ref="user" label-width="100px" class="userForm">

    <!--		<el-form-item label="账号">-->
		<el-form-item label="账号" prop="account">
      <!--<el-input v-model="user.account" placeholder="请输入你的邮箱"></el-input>-->
<!--      <el-input placeholder="请输入你的邮箱" v-model="user.account"></el-input>-->
      <el-input placeholder="请输入你的邮箱" v-model="user.account" autocomplete="off"></el-input>
    </el-form-item>

<!--		<el-form-item label="密码" prop="pass">-->
		<el-form-item label="密码" prop="password">
      <!--<el-input placeholder="请输入输入密码" type="password" v-model="user.password"></el-input>-->
      <el-input placeholder="请输入输入密码" type="password" v-model="user.password" autocomplete="off"></el-input>
    </el-form-item>

		<el-form-item label="验证码">
			<el-input v-model="user.code" placeholder="请输入验证码" autocomplete="off"></el-input>
			<img :src="captchaUrl" alt="验证码" @click="refreshCode" />
		</el-form-item>
		 <!--<div><img :src="captchaUrl" alt="验证码" @click="refreshCode" /></div>-->
		<el-form-item>
			<el-button type="primary" @click="login">提交</el-button>
			<el-button @click="resetForm('user')">重置</el-button>
		</el-form-item>
	</el-form>
</template>

<script>
const OK = 200;
export default {
	data() {
		return {
			rules: {},
			user: {},
			code: '',
			token: '',
			captchaUrl: ''
		};
	},
	methods: {
		login: function() {
			this.$axios
				.post('/api/users/login', this.user)
				.then(res => {
					console.log(this.user);
					// this.$layer.msg(res.data);
					console.log(res.data);
					if (res.data.code == OK) {
						this.$message.success('登录成功');
						this.$cookies.set('token', res.data.data, 60 * 30);
						this.token = res.data.data;
						console.log(this.token);
						this.$router.push({ name: 'Home' });
					} else {
						this.$message.error(res.data.message);
						this.refreshCode();
					}
				})
				.catch(function (error) {
				    this.$message.error(error);
        });
		},
		refreshCode: function() {
			this.captchaUrl = '/api/users/getKaptcha?time=' + new Date().getTime();
			console.log('更新' + this.captchaUrl);
		},
		resetForm: function() {
      // this.$refs[user].resetFields()
    },
		tryToAminPage: function() {
			var token = this.$cookies.get('token');
			if (token != null) this.$router.push({ name: 'Home' });
		},
    //初始化表单,方便测试
		test: function() {
			(this.user.account = 'test@163.com'), (this.user.password = '123456');
		}
	},
	created() {
		// this.tryToAminPage();
		// this.test();
		this.refreshCode();
	}
};
</script>

<style scoped="scoped">
.userForm {
	width: 550px;
	margin: 0 auto;
	padding: 80px;
}
</style>
