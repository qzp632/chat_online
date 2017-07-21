<template>
	<div>
		<div v-show="!ish">
			<p>在线聊天工具</p>
			<div class="adm">
				<label>账号：</label>
				<input class="inputText" v-model="username" type="text">
			</div>
			<div class="adm">
				<label>密码：</label>
				<input class="inputText" v-model="password" type="text">
			</div>
			<button class="login" @click="goLogin">登陆</button>
		</div>

		<div v-show="ish">
		<div>当前聊天室有{{count}}个人</div>
			<div class="box" >
				
				<div v-for="item in prs">
					<label v-if="item.user==username" style="background: red">我的</label>
					<label v-else style="background: green">{{item.user}}</label>
					<div v-if="item.user==username" style="margin-left: 20px;background: pink">{{item.msg}}</div>
					<div v-else  style="margin-left: 20px;background: yellow">{{item.msg}}</div>
				</div>
			</div>
			<input type="text" v-model="con">
			<button class="sedbtn" @click="goAdmin">提交</button>
		</div>
	</div>
</template>

<script>
	export default {
	  data() {
	  	return {
	  		username:"",
	  		password:"",
	  		socket:"",
	  		ish:false,
	  		con:"",
	  		user:"",
	  		content:"",
	  		prs:[],
	  		count:0
	  	}
	  },
	  mounted() {
	
	  	// this.socket.on('msg',function(data){
	  	// 	console.log(data)
	  	// })
	  	// this.socket.on('disconnect',function(){
	  	// 	console.log("断开连接")
	  	// })
	  	// this.socket.on('add',function(count){
	  	// 	console.log(count)
	  	// })
	  },
	  methods: {
	  	goLogin() {
	  		if(!this.username){
	  			alert("请输入正确的账户名")
	  			return
	  		}
	  		if(!this.password){
	  			alert("请输入正确的密码")
	  			return
	  		}
	  		if(this.username && this.password) {
	  			var self = this
		  		this.socket = io('ws://localhost:3000')
			  	this.socket.on('connect',function(){
				  	self.socket.emit('login',{
				  		username:self.username,
				  		password:self.password
				  	})
				  	self.ish = true
			  	})
			  	this.socket.on('msg',function(data){
					self.dataList(data)

			  	})
			  	this.socket.on('counts',function(count){
			  		self.count = count
			  	})			  	
	  		}
	  	},
	  	dataList(data){
	  		this.prs.push(data)

	  	},
	  	goAdmin() {
	  		this.socket.emit('send',this.con)
	  		this.con = ""

	  	}
	  }
	}
</script>

<style>
.adm {
	margin-top: 20px;
}
.inputText {
	border: 1px solid #ccc;
}
.login {
	font-size: 20px;
	border: 1px solid #888;
	background: #ccc;
	margin-top: 20px;
	outline: none;
}

.box {
	width: 100%;
	height: 400px;
	border: 1px solid #ccc;
}
.sedbtn {
	background: red
}
</style>