<template>
	<view>
		<el-container class="elcontainer">
			
			<el-header class="home-head">
				<el-row class="divHead">
				  <el-col :span="8" class="divItem"><div class="title">苦糖果MES-触控屏端</div></el-col>
				  <el-col :span="8" class="divItem"><div class="workstation">{{this.vuex_workstation==null?'':this.vuex_workstation.workstationName}}</div></el-col>
				  <el-col :span="8" class="divItem"><div class="user">
					<el-dropdown @command="handleCommand" style="float: right;">
						<span class="el-dropdown-link">
							{{'张三'}}<i><img :src="userInfo.avatar"></img></i>
						</span>
						<el-dropdown-menu slot="dropdown">
							<el-dropdown-item command="pwd">更改密码</el-dropdown-item>
							<el-dropdown-item command="workstation">切换工作站</el-dropdown-item>
							<el-dropdown-item command="exit">退出登录</el-dropdown-item>
						</el-dropdown-menu>
					</el-dropdown>
				  </div></el-col>
				</el-row>
				
				</div>
				<el-dialog title="请指定工作站" :visible.sync="open" width="800px" append-to-body>
					<el-tabs type="border-card" v-model="activeProcess" @tab-click="getWorkstationList">
						<el-tab-pane v-for="item in processList" :key="item.processId" :label="item.processName"
							:name="item.processCode">
							<el-card class="box-card" style="width: 300px" v-for="card in workstationList"
								:key="card.workstationId" :name="card.workstationCode">
								<div slot="header">
									<span>工作站{{card.workstationCode}}</span>
									<el-button type="primary" style="float:right;padding: 1px 0;"
										@click="setWorkstation(card)">选择</el-button>
								</div>
								{{'工作站名称：'+card.workstationName}}
							</el-card>
						</el-tab-pane>
					</el-tabs>
					<div slot="footer" class="dialog-footer">
						<el-button @click="cancel">关 闭</el-button>
					</div>
				</el-dialog>
			</el-header>
			<el-main class="mainContent">
				<TabHeader></TabHeader>
				<router-view></router-view>
			</el-main>
		</el-container>
	</view>
</template>

<script>
	import TabHeader from "./TabHeader.vue"
	export default {
		name: 'HomePage',
		components: {
			TabHeader
		},
		data() {
			return {
				loading: false,
				open: false,
				activeProcess: null,
				userInfo: {
					nickName: '张三',
					avatar: require("@/static/images/head.png"),
				},
				processList: [], //工序清单
				workstationList: [], //工作站清单
			}
		},
		created() {
			this.checkWorkstation();
			this.getProcessList();
		},
		methods: {
			//检查工作站设置情况
			checkWorkstation() {
				if (this.vuex_workstation == null) {
					this.open = true;
					this.$u.toast("请设置当前触控屏的工作站！");
				}
			},
			cancle() {
				this.open = false;
			},
			//获取工序清单
			getProcessList() {
				this.$u.api.getProcessList({}).then(res => {
					if (res.code == '200') {
						this.processList = res.data;
						this.activeProcess = res.data[0].processCode;
						this.getWorkstationList();
					} else {
						this.$u.toast("获取工序清单异常" + res.msg);
					}

				});
			},
			//获取工作站清单
			getWorkstationList() {
				this.$u.api.getWorkstationList({
					processCode: this.activeProcess
				}).then(res => {
					if (res.code == '200') {
						this.workstationList = res.data;
					} else {
						this.$u.toast("获取工作站清单异常" + res.msg);
					}
				});
			},
			//设置当前触控屏的工作站
			setWorkstation(station) {
				this.$u.vuex('vuex_workstation', station);
				this.open = false;
			},
			handleCommand(command) {
				if (command == 'exit') {
					this.loading = true;
					this.$u.api.logout().then(res => {
						this.loading = false;
						if (res.msg) {
							this.$u.toast(res.msg);
						}

						if (res.code == '200') {
							setTimeout(() => {
								uni.reLaunch({
									url: '/pages/sys/login/login'
								});
							}, 500);
						}
					})
				} else if (command == 'workstation') {

					this.open = true;
				}
			}
		}
	}
</script>

<style>
	.elcontainer {
		background: rgb(3 26 60);
	}

	.home-head {
		background-color: rgb(3, 26, 60);
		display: flex;
		align-items: center;
		
		padding: 0 15px;
		box-sizing: border-box;
	}
	
	.divHead{
		width: 100%;
		height: 100%;
		color: aliceblue;
		font-size: 25px;
		font-family: 华文楷体;
	}
	
	.divHead .divItem{
		display: inline-flex;
		height: 100%;
		align-items: center;
	}
	.divHead .title{
		float: left;
		width: 100%;
	}
	.divHead .workstation{
		text-align: center;
		width: 100%;
	}
	.divHead .user{
		
		width: 100%;
	}
	
	

	.el-dropdown-link {
		font-size: 20px;
		color: aliceblue;
		margin-bottom: 15px;
	}

	.el-dropdown-link img {
		width: 40px;
		height: 40px;
		border-radius: 20px;
		margin-left: 10px;
	}

	.mainContent {
		padding: 10px 10px 0px 10px;
		margin: 7px;
		background-image: linear-gradient(to top right, rgb(19 26 56), rgb(33 64 128));
	}
</style>
