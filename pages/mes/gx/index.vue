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
							<el-card class="box-card" v-for="item in stepData">
								<el-row>
								  <el-col :span="12" class="item-img">
									  <el-image :src="item.img" fit="contain"></el-image>
								  </el-col>
								  <el-col :span="12" class="item-text">
									  {{item.attention}}
								  </el-col>
								</el-row>
							</el-card>
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
						<el-button type="primary" icon="el-icon-video-play">选择领料单</el-button>
					</el-col>
				</el-row>
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
											<el-form-item label="单位">
												<el-input disabled v-model="item.unitOfMeasure"></el-input>
											</el-form-item>
										</el-col>
									</el-row>
									<el-row>
										<el-col :span="12">
											<el-form-item label="领料数量">
												<el-input disabled v-model="item.quantity_issued"></el-input>
											</el-form-item>
										</el-col>
										<el-col :span="12">
											<el-form-item label="已使用数量">
												<el-input disabled v-model="item.quantity_used"></el-input>
											</el-form-item>
										</el-col>
									</el-row>
									<el-row>
										<el-col :span="24">
											<el-button type="warning" icon="el-icon-delete">移除</el-button>
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
				open: false,
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
				stepData: [
					{
						img: 'https://cube.elemecdn.com/6/94/4d3ea53c084bad6931a56d5158a48jpeg.jpeg',
						attention: '注塑工序第一步说明'
					},
					{
						img: 'https://cube.elemecdn.com/6/94/4d3ea53c084bad6931a56d5158a48jpeg.jpeg',
						attention: '注塑工序第二步说明'
					},
					{
						img: 'https://cube.elemecdn.com/6/94/4d3ea53c084bad6931a56d5158a48jpeg.jpeg',
						attention: '注塑工序第三步说明'
					},
				],
				itemData: []
			}	
		},
		created(){
			if(this.vuex_task != null){
				this.getIssueList();
			}else{
				this.$u.toast('请在生产栏目切换生产任务！');
			}
		},
		methods: {
			//获取当前工作台、当前生产任务的投料清单
			getIssueList(){
				this.$u.api.getIssueList({
					workstationId: this.vuex_workstation.workstationId,
					taskId: this.vuex_task.taskId
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
		height: 300px;
	}
	
	.attention{
		height: 150px;
	}
	
	.item-img{
		width: 200px;
		height: 200px;
	}
</style>