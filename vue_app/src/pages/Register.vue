<template>
<!--  注册 -->
<div class="login">
	<Message-box :visible="this.messageBox.visible" :messageBoxEvent="this.messageBox.messageBoxEvent" @confirm="confirm" :hasCancel=false>
		<p slot="content">{{this.messageBox.message}}</p>
	</Message-box>
	<div class="wrapper fadeInDown">
		<div id="formContent">
			<router-link to="/login">
				<h2 class="inactive"> 登录 </h2>
			</router-link>
			<h2 class="active">注册 </h2>
			<div class="fadeIn first">
        <div class="fadeIn first">
          <svg id="icon" class="icon" alt="User Icon" aria-hidden="true">
            <use xlink:href="#iconliaotian"></use>
          </svg>
        </div>
			</div>
			<form>
				<div>
          <span class="normal-word">用户名：</span><input type="text" class="fadeIn second" v-model="name" placeholder="用户名">
        </div>
        <div>
          <span class="normal-word">密码：</span><input type="password" class="fadeIn third" v-model="password" placeholder="密码">
        </div>
				<div>
          <span class="normal-word">邮箱：</span><input type="text" class="fadeIn third" v-model="email" placeholder="邮箱">
        </div>
				<input type="button" :disabed="disabledFlag" @click="startRegister" class="fadeIn fourth" value="注册">
			</form>
		</div>
	</div>
</div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex';
import { debounce, checkEmail } from "../utils/common"
export default {
	data() {
		return {
			name: '',
			password: '',
      email: '',
			messageBox: {
				visible: false,
				message: "", //弹窗内容
				hasCancel: true, //弹窗是否有取消键
				messageBoxEvent: "" // 弹窗事件名称
			},
      disabledFlag: false
		}
	},
	methods: {
    ...mapActions(["register","activateEmail"]),
	  actualRegister() {
	    let params = {
	      name: this.name,
        password: this.password,
        email: this.email
      }
      if (this.name !== "" && this.password !== "" && this.email !== "" && checkEmail(this.email)) {
        this.register(params).then(res => {
          if (res) {
            if (res.success) {
              //弹窗
              this.messageBox.messageBoxEvent = 'register'
              this.messageBox.visible = true;
              this.messageBox.message = "您已注册成功";
            } else {
              this.$message({
                message: res.message,
                type: "error"
              });
            }
          }
        }).catch(err => {
          console.log(err)
          this.$message({
            message: '服务器出错啦',
            type: "error"
          });
        })
      } else {
        let message;
        if (this.name === "") { message = "请输入用户名" }
        if (this.password === "") { message = "请输入密码" }
        if (this.email === "") { message = "请输入邮箱" }
        if (!checkEmail(this.email)) { message = "请输入正确的邮箱" }
        this.$message({
          message: message,
          type: "warn"
        });
      }
    },
    startRegister() {
      this.disabledFlag = true;
      this.actualRegister()
      this.disabledFlag = false;
		},
		confirm(value) {
			if (value === 'register') {
				this.messageBox.visible = false;
				this.$router.push("/login");
			}
		}
	}
}
</script>

<style lang="scss" scoped>
@import '../assets/css/loginregister.scss';
</style>
