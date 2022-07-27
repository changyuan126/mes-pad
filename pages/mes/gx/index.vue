<template>
	<view class="commonBody">
		<el-row :gutter="20">
			<el-col :span="10">
				<el-row >
					<el-col :span="24">
						<el-card shadow="never" class="attention">
							 <div slot="header" class="clearfix">
							    <span>{{"当前工序："+process.name}}</span>							    
							  </div>
							{{"工艺要求："+process.attention}}
						</el-card>						
					</el-col>
				</el-row>
				<el-row>
					<el-col :span="24">
						<scroll-view scroll-y="true" class="scroll-Y">
							<div v-for="(item,index) in sopList" :key="index" class="image-middle">
								<el-card shadow="hover" :body-style="{pading: '10px'}">
									<el-popover>
										<img :src="sopList[index].sopUrl" slot="reference" class="image"/>
										<el-image class="imagePreview" :src="sopList[index].sopUrl" :preview-src-list="imageList"></el-image>                        
									</el-popover>
									<div style="text-align:center;padding-top:12px">
										<span>
											{{sopList[index].sopDescription}}
										</span>
									</div>
								</el-card>
							</div>
						</scroll-view>
					</el-col>
				</el-row>				
			</el-col>
			<el-col :span="14">
				<el-row>
					<el-col :span="4">
						{{"当前生产批次："}}
					</el-col>
					<el-col :span="8">						
						<el-input v-model="batchCode"></el-input>						
					</el-col>
					<el-col :span="6">
						<el-button type="primary" icon="el-icon-video-play">来料扫码</el-button>
					</el-col>
					<el-col :span="6">
						<el-button type="primary" @click="handleIssueShow" icon="el-icon-video-play">选择领料单</el-button>
					</el-col>
				</el-row>
				<el-dialog title="选择生产领料" :visible.sync="issueOpen" width="800px" append-to-body>
					<el-tabs type="border-card">
						<el-tab-pane label="领用到工作站">
							<el-table v-loading="loading" :data="issueWSList">
								<el-table-column label="批次号" align="center" prop="batchCode" />
								<el-table-column label="产品物料编码" width="120px" align="center" prop="itemCode" />
								<el-table-column label="产品物料名称" width="120px"  align="center" prop="itemName" :show-overflow-tooltip="true"/>
								<el-table-column label="仓库名称" align="center" prop="warehouseName" /> 												
								<el-table-column label="领料总数量" align="center" prop="quantityIssued" />
								<el-table-column label="操作" align="center" width="100px" class-name="small-padding fixed-width">
									<template slot-scope="scope">
									  <el-button
										size="mini"
										type="text"
										icon="el-icon-circle-check"
										@click="handleAdd(scope.row)"						            
									  >使用</el-button>
									</template>
								</el-table-column>
							</el-table>							
						</el-tab-pane>
						<el-tab-pane label="领用到生产工单">
							<el-table v-loading="loading" :data="issueWOList">
								<el-table-column label="批次号" align="center" prop="batchCode" />
								<el-table-column label="产品物料编码" width="120px" align="center" prop="itemCode" />
								<el-table-column label="产品物料名称" width="120px"  align="center" prop="itemName" :show-overflow-tooltip="true"/>
								<el-table-column label="仓库名称" align="center" prop="warehouseName" /> 												
								<el-table-column label="领料总数量" align="center" prop="quantityIssued" />
								<el-table-column label="操作" align="center" width="100px" class-name="small-padding fixed-width">
									<template slot-scope="scope">
									  <el-button
										size="mini"			
										icon="el-icon-circle-check"
										@click="handleAdd(scope.row)"						            
									  >使用</el-button>
									</template>
								</el-table-column>
							</el-table>	
						</el-tab-pane>
					</el-tabs>
					
					<div slot="footer" class="dialog-footer">													
						<el-button @click="handleCancle">关 闭</el-button>
					</div>
				</el-dialog>
				<el-divider></el-divider>
				<el-row>
					<el-col :span="24">
						<scroll-view scroll-y="true" class="scroll-item">
							<el-card class="box-card" v-for="item in itemData">
								<el-form :inline="true">
									<el-row>
									  <el-col :span="12">
										  <el-form-item label="物料编码" prop="quantity">
										  	<el-input disabled v-model="item.itemCode"></el-input>
										  </el-form-item>
									  </el-col>
									  <el-col :span="12">
										  <el-form-item label="物料名称">
										  	<el-input disabled v-model="item.itemName"></el-input>
										  </el-form-item>
									  </el-col>								  
									</el-row>
									<el-row>
										<el-col :span="12">
											<el-form-item label="规格型号">
												<el-input disabled v-model="item.spc"></el-input>
											</el-form-item>
										</el-col>
										<el-col :span="12">
											<el-form-item label="单 位">
												<el-input disabled v-model="item.unitOfMeasure"></el-input>
											</el-form-item>
										</el-col>
									</el-row>
									<el-row>
										<el-col :span="12">
											<el-form-item label="领料数量">
												<el-input disabled v-model="item.quantityIssued"></el-input>
											</el-form-item>
										</el-col>
										<el-col :span="12">
											<el-form-item label="已使用数量">
												<el-input disabled v-model="item.quantityUsed"></el-input>
											</el-form-item>
										</el-col>
									</el-row>
									<el-row>
										<el-col :span="24">
											<el-button type="warning" @click="handleRemove(item)" icon="el-icon-delete">移除</el-button>
										</el-col>
									</el-row>
								</el-form>								
							</el-card>
						</scroll-view>
					</el-col>
				</el-row>
				<el-divider></el-divider>
				<el-row>
					<el-col :span="24">
						<el-button type="primary" @click="printTrans" icon="el-icon-printer">打印流转单</el-button>
					</el-col>
				</el-row>
				<el-dialog :title="title" :visible.sync="open" width="800px" append-to-body>
					<el-form ref="trans" :model="transForm" :rules="rules" label-width="100px">
						<el-row>
							<el-col :span="12">
								<el-form-item label="产品名称" prop="itemName">
								    <el-input  v-model="transForm.itemName" />
								</el-form-item>
							</el-col>
							<el-col :span="12">
								<el-form-item label="产品编码" prop="itemCode">
								    <el-input  v-model="transForm.itemCode" />
								</el-form-item>
							</el-col>
						</el-row>
						<el-row>								
							<el-col :span="12">
								<el-form-item label="单位" prop="unitOfMeasure">
								    <el-input  v-model="transForm.unitOfMeasure" />
								</el-form-item>
							</el-col>	
							<el-col :span="12">
								<el-form-item label="任务编号" prop="workTaskCode">
									<el-input  v-model="transForm.taskCode" />
								</el-form-item>
							</el-col>
						</el-row>
						<el-row>
							<el-col :span="12">
								<el-form-item label="流转数量:" prop="quantity_transfered">
									<el-input-number v-model="transForm.quantity_transfered"></el-input-number>
								</el-form-item>
							</el-col>
							<el-col :span="12">
								<el-form-item label="是否报工:">
									<el-switch
									  v-model="feedbackFlag"
									  active-text="是"
									  active-value="Y"
									  inactive-text="否"
									  inactive-value="N"
									  >
									</el-switch>									
								</el-form-item>
							</el-col>
						</el-row>
					</el-form>
					<div slot="footer" class="dialog-footer">
						<el-button type="primary" @click="submit()" >提 交</el-button>								
						<el-button @click="cancel">取 消</el-button>
					</div>
				</el-dialog>
			</el-col>
		</el-row>
	</view>
</template>

<script>
	export default{
		data(){
			return {
				title: "流转单打印",
				loading: false,
				open: false,
				issueOpen: false,
				feedbackFlag: 'Y', //是否在打印流转单的同时报工
				batchCode:'202207191001',
				transForm: {},
				rules: {
					quantity_transfered: [
						{ required: true, message: "请输入本次报工数量！", trigger: "blur" }
					]
				},
				process: {
					name: "注塑",
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
				this.getSopList();
				this.getIssueList();
			}else{
				this.$u.toast('请在生产栏目切换生产任务！');
			}
		},
		methods: {
			//获取当前产品的SOP
			getSopList(){
				let that = this;
				this.$u.api.getSopList({
					itemId: this.vuex_task.itemId								
				}).then( res =>{
						if(res.code == '200'){
							debugger;
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
	
	.scroll-Y{
		height: 450px;
	}
	
	.scroll-item{
		height: 400px;
	}
	
	.attention{
		height: 150px;
	}
	
	.item-img{
		width: 200px;
		height: 200px;
	}
	
	    .image-middle{
	        margin-right: 15px;
	        margin-bottom: 15px;
	    }
	
	    .image{
	        width:110px;
	        height: 110px;
	    }
	
	    .imagePreview {
	        width: 600px;
	        height: 500px;
	    }
</style>