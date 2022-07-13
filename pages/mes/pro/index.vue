<template>
	<view class="commonBody">
		<el-row :gutter="20">
			<el-col :span="10">
				<scroll-view scroll-y="true" class="scroll-Y">
					<el-table
					  :data="tableData"
					  style="width: 100%">
					    <el-table-column
							prop="itemName"
							label="产品名称"
							width="100">
					    </el-table-column>
						<el-table-column
							prop="quantity"
							label="任务数量"
							width="80">
						</el-table-column>
						<el-table-column label="排产时间" align="center" prop="startTime" width="100">
						    <template slot-scope="scope">
						        <span>{{ parseTime(scope.row.startTime, '{y}-{m}-{d}') }}</span>
						    </template>
						</el-table-column>
						<el-table-column
							prop="address"
							label="操作">
							<template slot-scope="scope">
								<el-button
									size="mini"	
									type="primary"
									icon="el-icon-refresh"
									@click="shiftTask(scope.row.taskId)"
								  >切换</el-button>
							</template>
						</el-table-column>
					</el-table>
				</scroll-view>				
			</el-col>
			<el-col :span="14">
				<el-row :gutter="20" style="margin-top: 20px;">
					<el-col :span="6">
						<el-button type="primary" v-if="form.status =='NORMAL'" @click="changeStatus('START')" icon="el-icon-video-play">开始</el-button>
						<el-button type="primary" v-else-if="form.status =='START'" @click="changeStatus('PAUSE')" icon="el-icon-video-play">暂停</el-button>
						<el-button type="primary" v-else-if="form.status =='PAUSE'" @click="changeStatus('START')" icon="el-icon-video-play">继续</el-button>
					</el-col>
					<el-col :span="6">
						<el-button type="primary" v-if="form.taskId !=null " @click="changeStatus('FINISHED')" icon="el-icon-video-pause">完成</el-button>
					</el-col>
					<el-col :span="6">
						<el-button type="primary" @click="doFeedback" v-if="form.taskId !=null && form.status !='NORMAL'" icon="el-icon-edit-outline">生产报工</el-button>
						<el-dialog :title="title" :visible.sync="open" width="800px" append-to-body>
							<el-form ref="feedback" :model="feedbackForm" :rules="rules" label-width="100px">
								<el-row>
									<el-col :span="12">
										<el-form-item label="报工总数量" prop="quantity">
											<el-input-number disabled v-model="feedbackForm.quantity"></el-input-number>
										</el-form-item>
									</el-col>
									<el-col :span="12">
										<el-form-item label="报工人" prop="nickName">
											<el-input v-model="feedbackForm.nickName"></el-input>
										</el-form-item>
									</el-col>
								</el-row>
								<el-row>
									<el-col :span="12">
										<el-form-item label="合格品数量" prop="quantityQualify">
											<el-input-number :min="0" @change="quantityChanged" v-model="feedbackForm.quantityQualify"></el-input-number>
										</el-form-item>
									</el-col>
									<el-col :span="12">
										<el-form-item label="不良品数量" prop="quantityUnqualify">
											<el-input-number :min="0" @change="quantityChanged" v-model="feedbackForm.quantityUnqualify"></el-input-number>
										</el-form-item>
									</el-col>
								</el-row>																							
							</el-form>
							<div slot="footer" class="dialog-footer">								
								<el-button type="primary" @click="feedback()" >提 交</el-button>								
								<el-button @click="cancel">取 消</el-button>
							</div>
						</el-dialog>
					</el-col>
					<el-col :span="6"></el-col>
				</el-row>
				<el-row :gutter="20">
					<el-col :span="24">
						<el-progress :text-inside="true" :stroke-width="26" :percentage="form.progress" status="success"></el-progress>
					</el-col>					
				</el-row>
				<el-row :gutter="20">
					<el-col :span="24">
						<el-form :inline="true" label-width="80px" size="small" :model="form" >
							<el-row>
								<el-col :span="12">
									<el-form-item label="产品名称" prop="itemName">
									    <el-input  v-model="form.itemName" />
									</el-form-item>
								</el-col>
								<el-col :span="12">
									<el-form-item label="产品编码" prop="itemCode">
									    <el-input  v-model="form.itemCode" />
									</el-form-item>
								</el-col>
							</el-row>
							<el-row>								
								<el-col :span="12">
									<el-form-item label="单位" prop="unitOfMeasure">
									    <el-input  v-model="form.unitOfMeasure" />
									</el-form-item>
								</el-col>	
								<el-col :span="12">
									<el-form-item label="任务编号" prop="workTaskCode">
										<el-input  v-model="form.taskCode" />
									</el-form-item>
								</el-col>
							</el-row>
							<el-row>
								<el-col :span="12">
									<el-form-item label="任务数量" prop="quantity">
										<el-input  v-model="form.quantity" />
									</el-form-item>
								</el-col>
								<el-col :span="12">
									<el-form-item label="生产数量" prop="quantityProduced">
										<el-input  v-model="form.quantityProduced" />
									</el-form-item>
								</el-col>
							</el-row>
							<el-row>
								<el-col :span="12">
									<el-form-item label="良品数量" prop="quantityQuanlify">
										<el-input  v-model="form.quantityQuanlify" />
									</el-form-item>
								</el-col>
								<el-col :span="12">
									<el-form-item label="不良数量" prop="quantityUnquanlify">
										<el-input  v-model="form.quantityUnquanlify" />
									</el-form-item>
								</el-col>
							</el-row>
						</el-form>
					</el-col>
				</el-row>
				<el-row>
					<el-col :span="8">
						<el-button type="primary" icon="el-icon-video-play">SOP</el-button>
					</el-col>
					<el-col :span="8">
						<el-button type="primary" icon="el-icon-video-play">SIP</el-button>
					</el-col>					
				</el-row>
			</el-col>
		</el-row>
	</view>
</template>

<script>
	export default{
		data(){
			return {
				title: '生产报工',
				open: false,
				form: {},
				feedbackForm:{},
				tableData: [],
				rules: {
					quantity: [
						{ required: true, message: "请输入合格品数量或者不良品数量！", trigger: "blur" }
					]
				}
			}
		},
		created(){
			this.getTaskList();
		},
		methods:{
			getTaskList(){
				this.$u.api.getTaskList({
					workstationId: this.vuex_workstation.workstationId,					
				}).then( res =>{
						if(res.code == '200'){
							this.tableData = res.data;
						}
					}				
				);
			},
			shiftTask(tid){
				this.$u.api.getTaskInfo({
					taskId: tid,					
				}).then( res =>{
						if(res.code == '200'){
							this.$u.vuex('vuex_task',res.data);
							this.form = res.data;
							this.form.progress = Math.round(res.data.quantityProduced/res.data.quantity,0);
						}
					}				
				);
			},
			changeStatus(status){
				this.form.status = status;
				this.$u.api.changeStatus({
					taskId: this.form.taskId,
					status: status
				}).then( res =>{
						if(res.code == '200'){		
							this.getTaskList();
							if(status=='FINISHED'){
								this.$u.vuex('vuex_task',null);
								this.form ={};
							}							
							this.$u.toast('变更成功');
						}
					}				
				);
			},
			reset(){
				this.feedbackForm ={
					workstationId: 200,
					userName:'admin',
					taskId: this.form.taskId,
					feedbackChannel:'PAD',
					quantity: 0,
					quantityQualify: 0,
					quantityUnqualify: 0
				}
			},
			quantityChanged(){
				this.feedbackForm.quantity = this.feedbackForm.quantityQualify + this.feedbackForm.quantityUnqualify;
			},
			doFeedback(){
				this.reset();
				this.feedbackForm.nickName = this.vuex_user.nickName;
				this.feedbackForm.userName = this.vuex_user.userName;
				this.open = true;
			},
			cancel(){
				this.open = false;
			},
			feedback(){
				this.$u.api.feedback({
					taskId: this.form.taskId,
					quantityFeedback: this.feedbackForm.quantity,
					quantityQualified: this.feedbackForm.quantityQualify,
					quantityUnquanlified: this.feedbackForm.quantityUnqualify,
					userName: this.vuex_user.userName
				}).then( res =>{
						if(res.code == '200'){							
							this.$u.toast('上报成功');
							this.shiftTask(this.form.taskId)
						}						
					}				
				);
				this.open = false;
			}
		}
	}
</script>

<style>
	.commonBody{
		background-color: #FEFEFF;
		
	}
	
	.scroll-Y{
		height: 500px;
	}
	
	.el-row{
		margin-bottom: 20px;
	}
</style>