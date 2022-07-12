<template>
	<view class="login">
		<el-form ref="loginForm" :model="loginForm" :rules="loginRules" class="login-form">
		  <h3 class="title">苦糖果MES</h3>
		  <el-form-item prop="username">
		    <el-input
		      v-model="loginForm.username"
		      type="text"
		      auto-complete="off"
		      placeholder="账号"
		    >
		      <svg-icon slot="prefix" icon-class="user" class="el-input__icon input-icon" />
		    </el-input>
		  </el-form-item>
		  <el-form-item prop="password">
		    <el-input
		      v-model="loginForm.password"
		      type="password"
		      auto-complete="off"
		      placeholder="密码"
		      @keyup.enter.native="submit"
		    >
		      <svg-icon slot="prefix" icon-class="password" class="el-input__icon input-icon" />
		    </el-input>
		  </el-form-item>
		  <el-form-item style="width:100%;">
		    <el-button
		      :loading="loading"
		      size="medium"
		      type="primary"
		      style="width:100%;"
		      @click.native.prevent="submit()"
		    >
		      <span v-if="!loading">登 录</span>
		      <span v-else>登 录 中...</span>
		    </el-button>
		  </el-form-item>
		</el-form>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				loading: false,
				loginForm: {
							username: "admin",
							password: "admin123",
							rememberMe: false,
							validCode:'',
							uuid: ""
				},
				loginRules: {
							username: [
							  { required: true, trigger: "blur", message: "请输入您的账号" }
							],
							password: [
							  { required: true, trigger: "blur", message: "请输入您的密码" }
							]
				},
			}
		},
		methods: {
			submit(){
				this.$refs.loginForm.validate(valid => {
					if (valid) {
					  this.loading = true;
					  this.$u.api.login({
					  	username: this.loginForm.username,
					  	password: this.loginForm.password,
					  	validCode: this.loginForm.validCode,
					  	loginType: '1'
					  })
					  .then(res => {
						this.loading = false;
					  	if(res.msg){
					  		this.$u.toast(res.msg);
					  	}
					  	if (res.code == '200') {
					  		setTimeout(() => {
					  			uni.reLaunch({
					  				url: '/pages/index/index'
					  			});
					  		}, 500);
					  	}
					  });
					}
				});    
			}			
		}
	}
</script>

<style>
page {
    background-image: url("@/static/images/login-background.jpg");
    background-size: 100% 100%;
    width: 100%;  height: 100%;  
}

.login {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
}

.title {
  margin: 0px auto 30px auto;
  text-align: center;
  color: #707070;
}

.login-form {
  border-radius: 6px;
  background: #ffffff;
  width: 400px;
  padding: 25px 25px 5px 25px;
  .el-input {
    height: 38px;
    input {
      height: 38px;
    }
  }
  .input-icon {
    height: 39px;
    width: 14px;
    margin-left: 2px;
  }
}
</style>
