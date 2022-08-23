<template>
	<view class="commonBody">
		<u-row :gutter="20">
			<u-col :span="5">
				<div class="leftSide">
					<u-card :title="'当前工序：'+process.processName"  box-shadow="0 4px 8px rgba(0, 0, 0, 0.2)" class="attention">
						<view slot="body">
							<view class="">
								{{"工艺要求："+process.attention}}
							</view>
							<scroll-view scroll-y="true" class="scroll-Y">
								<div v-for="(item,index) in sopList" :key="index" class="sopDiv">
									<u-card shadow="hover" class="sop-card">
										<div style="float:left;margin: 0 0;">
											<u-popup>
												<img :src="sopList[index].sopUrl" slot="reference" class="image"/>
												<u-image class="imagePreview" :src="sopList[index].sopUrl" :preview-src-list="imageList"></u-image>                        
											</u-popup>
										</div>
										
										<div style="float: right;text-align:center;">
											<u-input type="textarea" style="height: 100%;" v-model="sopList[index].sopDescription">											
											</u-input>								
										</div>
									</u-card>
								</div>
							</scroll-view>
						</view>					
					</u-card>					
				</div>																		
			</u-col>
			<u-col :span="7">
				<u-row>
					<u-col :span="12">
						<div class="batch_head">
							<span>{{"当前生产批次："}}</span>
							<u-input style="width: 180px;" v-model="batchCode"></u-input>						
							<u-button type="primary" style="float: right; margin:auto 5px" icon="el-icon-video-play">来料扫码</u-button>
							<u-button type="primary" style="float: right; " @click="handleIssueShow" icon="el-icon-video-play">选择领料单</u-button>
						</div>						
					</u-col>
				</u-row>
				<u-modal title="选择生产领料" v-model="issueOpen" width="800px">
					<u-tabs :list="tabList" :current="currentTab" type="border-card" />
					<view v-if="currentTab == 0">
						<u-table  >
							<u-tr>
								<u-th>批次号</u-th>
								<u-th>产品物料编码</u-th>
								<u-th>产品物料名称</u-th>
								<u-th>仓库名称</u-th>
								<u-th>领料总数量</u-th>
								<u-th>操作</u-th>
							</u-tr>
							<u-tr :key="issueCode" v-for="issue in issueWSList">
								<u-th>{{issue.batchCode}}</u-th>
								<u-th>{{issue.itemCode}}</u-th>
								<u-th>{{issue.itemName}}</u-th>
								<u-th>{{issue.warehouseName}}</u-th>
								<u-th>{{issue.quantityIssued}}</u-th>
								<u-th><u-button
									size="mini"
									type="text"
									@click="handleAdd(issue)"						            
								  >使用</u-button>
								</u-th>
							</u-tr>
						</u-table>							
					</view>
					<view  v-else>
						<u-table  >
							<u-tr>
								<u-th>批次号</u-th>
								<u-th>产品物料编码</u-th>
								<u-th>产品物料名称</u-th>
								<u-th>仓库名称</u-th>
								<u-th>领料总数量</u-th>
								<u-th>操作</u-th>
							</u-tr>
							<u-tr :key="issueCode" v-for="issue in issueWOList">
								<u-th>{{issue.batchCode}}</u-th>
								<u-th>{{issue.itemCode}}</u-th>
								<u-th>{{issue.itemName}}</u-th>
								<u-th>{{issue.warehouseName}}</u-th>
								<u-th>{{issue.quantityIssued}}</u-th>
								<u-th><u-button
									size="mini"
									type="text"
									@click="handleAdd(issue)"						            
								  >使用</u-button>
								</u-th>
							</u-tr>
						</u-table>									
					</view>
				</u-modal>
				<u-row>
					<u-col :span="12">
						<scroll-view scroll-y="true" class="scroll-item">
							<div class="info_card" v-for="item in itemData">
								<div class="issue_info">
									<u-row>
										<u-col :span="6">
											<span class="info_label">物料编码：</span>{{item.itemCode}}
										</u-col>
										<u-col :span="6">
											<span class="info_label">物料名称：</span>{{item.itemName}}
										</u-col>
									</u-row>
									<u-row>
										<u-col :span="5">
											<span class="info_label">规格型号：</span>{{item.spc}}
										</u-col>
										<u-col :span="3">
											<span class="info_label">单位：</span>{{item.unitOfMeasure}}
										</u-col>
										<u-col :span="4">
											<span class="info_label">投料数量：</span>{{item.quantityIssued}}	
										</u-col>
									</u-row>
								</div>
								<div class="issue_bt">
									<u-button type="warning" @click="handleRemove(item)" icon="el-icon-delete">移除</u-button>
								</div>	
							</div>
						</scroll-view>
					</u-col>
				</u-row>
				<u-row>
					<u-col :span="12">
						<u-button type="primary" @click="printTrans" icon="el-icon-printer">打印流转单</u-button>
					</u-col>
				</u-row>
				<u-modal :title="title" v-model="open" width="800px" >
					<u-form ref="trans" :model="transForm" :rules="rules" label-width="100px">
						<u-row>
							<u-col :span="12">
								<u-form-item label="产品名称" prop="itemName">
								    <u-input  v-model="transForm.itemName" />
								</u-form-item>
							</u-col>
							<u-col :span="12">
								<u-form-item label="产品编码" prop="itemCode">
								    <u-input  v-model="transForm.itemCode" />
								</u-form-item>
							</u-col>
						</u-row>
						<u-row>								
							<u-col :span="12">
								<u-form-item label="单位" prop="unitOfMeasure">
								    <u-input  v-model="transForm.unitOfMeasure" />
								</u-form-item>
							</u-col>	
							<u-col :span="12">
								<u-form-item label="任务编号" prop="workTaskCode">
									<u-input  v-model="transForm.taskCode" />
								</u-form-item>
							</u-col>
						</u-row>
						<u-row>
							<u-col :span="12">
								<u-form-item label="流转数量:" prop="quantity_transfered">
									<u-number-box v-model="transForm.quantity_transfered"></u-number-box>
								</u-form-item>
							</u-col>
							<u-col :span="12">
								<u-form-item label="是否报工:">
									<u-switch
									  v-model="feedbackFlag"
									  active-text="是"
									  active-value="Y"
									  inactive-text="否"
									  inactive-value="N"
									  >
									</u-switch>									
								</u-form-item>
							</u-col>
						</u-row>
					</u-form>
					<div slot="footer" class="dialog-footer">
						<u-button type="primary" @click="submit()" >提 交</u-button>								
						<u-button @click="cancel">取 消</u-button>
					</div>
				</u-modal>
			</u-col>
		</u-row>
	</view>
</template>

<script>
	export default{
		name: "GxContent",
		data(){
			return {
				title: "流转单打印",
				loading: false,
				open: false,
				issueOpen: false,
				feedbackFlag: 'Y', //是否在打印流转单的同时报工
				batchCode:'202207191001',
				tabList:[{
					name:"领用到工作站"
				},{
					name:"领用到生产工单"
				}],
				currentTab: 0,
				transForm: {},
				rules: {
					quantity_transfered: [
						{ required: true, message: "请输入本次报工数量！", trigger: "blur" }
					]
				},
				process: {
					processId: 0,
					processCode: '',
					processName: "注塑",
					attention: "注塑工序说明"
				},
				sopList: [],
				imageList: [],
				issueWSList: [],
				issueWOList: [],
				itemData: []
			}	
		},
		created(){
			if(this.vuex_task != null){	
				this.getProcessInfo();
				this.getSopList();
				this.getIssueList();
			}else{
				this.$u.toast('请在生产栏目切换生产任务！');
			}
		},
		methods: {
			//获取当前工序的信息
			getProcessInfo(){
				if(this.vuex_task != null){	
					let that = this;
					this.$u.api.getProcessInfo({
						processId: this.vuex_workstation.processId								
					}).then( res =>{
						debugger;
							if(res.code == '200'){
								that.process = res.data;
							}
						}				
					);
				}
			},
			
			//获取当前产品的SOP
			getSopList(){
				let that = this;
				this.$u.api.getSopList({
					itemId: this.vuex_task.itemId								
				}).then( res =>{
						if(res.code == '200'){
							that.imageList = [];
							that.sopList = res.data;
							that.sopList.forEach(row => {
							    that.imageList.push(row.sopUrl);
							});							
						}
					}				
				);
			},
			handleIssueShow(){
				this.getReserveIssue();
				this.issueOpen = true;
			},
			//获取当前工作台可用的领料单
			getReserveIssue(){				
				this.$u.api.getReserveIssue({
					workstationId: this.vuex_workstation.workstationId								
				}).then( res =>{
						if(res.code == '200'){
							this.issueWSList = res.data;							
						}
					}				
				);
			},
			//获取当前生产任务可用的领料单
			getReserveIssue2(){				
				this.$u.api.getReserveIssue({					
					workorderId: this.vuex_task.workorderId
				}).then( res =>{
						if(res.code == '200'){
							this.issueWOList = res.data;							
						}
					}				
				);
			},
			//添加领料单的某行到当前的投料清单
			handleAdd(row){				
				this.$u.api.addIssue({
					taskId: this.vuex_task.taskId,
					workstationId: this.vuex_workstation.workstationId,
					sourceDocType: 'ISSUE',
					sourceLineId: row.lineId
				}).then( res =>{
						if(res.code == '200'){
							this.$u.toast("操作成功!");
							this.getIssueList();//刷新投料清单								
						}else{
							this.$u.toast(res.msg);
						}						
					}				
				);
			},
			// 从投料清单中删除一条
			handleRemove(item){
				this.$u.api.removeTaskIssue({
					recordId: item.recordId
				}).then( res =>{
						if(res.code == '200'){
							this.getIssueList();//刷新投料清单	
							this.$u.toast("操作成功!");
						}else{
							this.$u.toast(res.msg);
						}
					}				
				);
			},
			handleCancle(){
				this.issueOpen = false;
			},
			//获取当前工作台、当前生产任务的投料清单
			getIssueList(){
				this.$u.api.getTaskIssueList({					
					workstationId: this.vuex_workstation.workstationId
				}).then( res =>{
						if(res.code == '200'){
							this.itemData = res.data;							
						}
					}				
				);
			},
			//点击“打印流转单”
			printTrans(){
				this.open = true;
			},
			//提交
			submit(){
				this.open = false;
			},
			//取消
			cancel(){
				this.open = false;
				this.feedbackFlag= true;//默认值至为true
			}
		}
	}
</script>

<style>
	.commonBody{
		background-color: #FEFEFF;
		
	}
	
	.sop-card {
		width: 100%;
	}
	
	.scroll-Y{
		height: 450px;
	}
	
	.scroll-item{
		height: 400px;
	}
	
	.card_head{
		height: 15px;
	}
	
	.el-card__header{
		background-color: palegoldenrod;
	}
	
	.attention{
		height: 100%;
		margin-left: 5px;
		margin-top: 5px;
		margin-bottom: 5px;
	}
	
	.item-img{
		width: 200px;
		height: 200px;
	}
	
	    .sopDiv{
			width: 100%;
	    }
	
	    .image{
	        width:110px;
	        height: 110px;
	    }
	
	    .imagePreview {
	        width: 600px;
	        height: 500px;
	    }
		
		.batch_head {
			margin-top: 5px;
			margin-bottom: 5px;
		}
		
		.info_card {
			height: 50px;
			margin: 5px;
			display: flex;
			box-shadow: 0px 0px 10px 5px rgba(0, 0, 0, 0.3);
		}
		
		.issue_info {			
			width: 435px;
		}
		
		.info_label {
			font-weight: bold;
		}
		
		.issue_bt {			
			width: 80px;
		}
		
</style>