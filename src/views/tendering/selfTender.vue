<template>
	<!-- 自行招标 -->
	<div class="newBox">
		<div class="wrapper">
			<!-- 搜索的输入框 -->
            <div class="editItem">
                <div class="editCenter">
                    <div class="editName">招标项目(分包)名称：</div>
                    <div class="editContent">
                        <el-input v-model="nameItem" clearable placeholder="请输入内容" @keyup.enter.native="searchList"></el-input>
                    </div>
                </div>
                <div class="editCenter">
                    <div class="editName">入场项目(分包)编号：</div>
                    <div class="editContent">
                        <el-input v-model="numberItem" clearable placeholder="请输入内容" @keyup.enter.native="searchList"></el-input>
                     </div>
                </div>
                <div class="editBtn">
                    <button class="btn" @click="searchList">搜索</button>
                </div>
            </div>
			<div class="main_info">
				<el-table
					:data="biddingData"
					style="width: 100%">
					<el-table-column type="index" label="序号" :index="typeIndex" width="70">
                    </el-table-column>
					<el-table-column
						prop="totalProjectId"
						:show-overflow-tooltip ="true"
						label="入场项目(分包)编号"
						width="180">
					</el-table-column>
					<el-table-column
						prop="nameOfTenderProject"
						label="招标项目名称"
						:show-overflow-tooltip ="true">
					</el-table-column>
					<el-table-column
						prop="biddingProjectNumber"
						label="招标项目编号"
						:show-overflow-tooltip ="true">
					</el-table-column>
					<el-table-column
						prop="subcontractName"
						label="分包名称"
						:show-overflow-tooltip ="true">
					</el-table-column>
					<el-table-column fixed="right" label="查看" width="100">
						<template slot-scope="scope">
							<i class="el-icon-search"  style="cursor: pointer;" @click="lookOver(scope.row)"></i>
						</template>
					</el-table-column>
					<el-table-column fixed="right" label="操作" width="100">
						<template slot-scope="scope">
							<i class="el-icon-edit"  style="cursor: pointer;" @click="handle(scope.row)"></i>
						</template>
					</el-table-column>
				</el-table>
			</div>
			<!-- 分页器 -->
           	<div class="pagination" v-if="this.pages>1">
				<el-pagination
					@size-change="handleSizeChange"
					@current-change="handleCurrentChange"
					:current-page.sync="currentPage"
					:page-size="pageSize"
					layout="total, prev, pager, next, jumper"
					:total="total">
				</el-pagination>
			</div>
		</div>

		<!-- 查看列表详情 -->
		<div class="detailWrapper" v-show="showLookover">
			<div class="detailBox">
				<div class="detail_title">
					<p>分包信息</p>
					<i class="el-icon-close"  @click="closeLook"></i>
				</div>
				<div class="main_info">
					<el-collapse v-model="activeName" accordion>
						<!-- 招标项目信息 -->
						<el-collapse-item title="招标项目信息" name="1">
							<div class="edit_list">
                                <div class="editItem">
                                    <div class="editCenter">
                                        <div class="editName">招标项目名称：</div>
                                        <div class="editContent">{{nameOfTenderProject}} </div>
                                    </div>
                                     <div class="editCenter">
                                        <div class="editName">招标项目编号：</div>
                                        <div class="editContent">{{biddingProjectNumber}} </div>
                                    </div>
                                </div>
                                <div class="editItem">
                                    <div class="editCenter">
                                        <div class="editName">招标(采购)人：</div>
                                        <div class="editContent">{{tenderPurchaserName}} </div>
                                    </div>
                                </div>
                                <div class="editItem">
                                    <div class="editCenter">
                                        <div class="editName">入场项目编号：</div>
                                        <div class="editContent">{{amountId}} </div>
                                    </div>
                                </div>
                            </div>
						</el-collapse-item>
						<!-- 分包信息 -->
						<el-collapse-item title="分包信息" name="2">
							<div class="edit_list">
								<div class="editItem">
									<div class="editCenter">
										<div class="editName" style="color:#f00;">标段包编号(赋码)：</div>
										<div class="editContent"></div>
									</div>
								</div>
								<div class="editItem">
									<div class="editCenter">
										<div class="editName" style="color:#f00;">统一交易标志码：</div>
										<div class="editContent"></div>
									</div>
								</div>
								<div class="editItem">
									<div class="editCenter">
										<div class="editName">入场项目分包编号：</div>
										<div class="editContent">{{entrySubcontractNumber}}</div>
									</div>
								</div>
								<div class="editItem">
									<div class="editCenter">
										<div class="editName">分包名称：</div>
										<div class="editContent" v-if="entrySubcontractNumber.indexOf('-') != -1">{{subcontractNameaa}} </div>
										<div class="editContent" v-else>{{nameOfTenderProject}} </div>
									</div>
								</div>
								<div class="editItem">
									<div class="editCenter">
										<div class="editName">分包内容：</div>
										<div class="editContent" v-if="entrySubcontractNumber.indexOf('-') != -1">{{subcontractContents}} </div>
										<div class="editContent" v-else>{{tenderContents}} </div>
									</div>
								</div>
								<div class="editItem">
									<div class="editCenter">
										<div class="editName">招标范围：</div>
										<div class="editContent">{{scopeOfTender}} </div>
									</div>
								</div>
								<div class="editItem">
									<div class="editCenter">
										<div class="editName" >分包控制金额：</div>
										<div class="editContent" v-if="entrySubcontractNumber.indexOf('-') != -1">{{subcontractingControlAmount}}万元</div>
										<div class="editContent" v-else>{{budgetAmount}}万元</div>
									</div>
									<div class="editCenter">
										<div class="editName">资格审查方式：</div>
										<div class="editContent" v-if="entrySubcontractNumber.indexOf('-') != -1">{{qualificationExaminationMethod}}</div>
										<div class="editContent" v-else>{{qualificationExaminationMethods}} </div>
									</div>
								</div>
								<div class="editItem">
									<div class="editCenter">
										<div class="editName" >是否电子招投标：</div>
										<div class="editContent" v-if="entrySubcontractNumber.indexOf('-') != -1">{{electronicBidEvaluation}} </div>
										<div class="editContent" v-else>{{electronicBidEvaluations}} </div>
									</div>
								</div>
							</div>
						</el-collapse-item>
					</el-collapse>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import '../../assets/css/style.css'
import baseUrl from '../../api/api'
export default {
	components:{},
	props:{},
	data(){
		return {
            role_types:window.localStorage.role_types,
            biddingPurchasingAgencyName:'',
			budgetAmount:'',
			tenderContents:'',
			activeName: ['1','2'],// 弹框折叠效果
			nameItem:'',//招标项目(分包)名称
			numberItem:'',//入场项目(分包)编号
			currentPage:1,// 当前页
			pageSize: 10, // 每页条数
            pageTotal: 0, //总页数
			total: 0, //总条数
			pages:'',
            biddingData: [],// 自行招标列表数据
			showLookover:false,// 查看列表详情
			titleName:'',// 判断是否是总包分包
			// 招标项目信息所需字段
			nameOfTenderProject:'',// 招标项目名称
			biddingProjectNumber:'',// 招标项目编号
			tenderPurchaserName:'',// 招标(采购)人
			totalProjectId:'',// 入场项目编号
			// 分包信息所需字段
			entrySubcontractNumber:'',// 入场项目分包编号
			subcontractName:'',// 分包名称
			subcontractNameaa:'',
			subcontractContents:'',//分包内容
			scopeOfTender:'',// 招标范围
			subcontractingControlAmount:0,// 分包控制金额
			qualificationExaminationMethod:'',// 资格审查方式
			electronicBidEvaluation:'',// 电子化开评标
			qualificationExaminationMethods:'',// 资格审查方式
			electronicBidEvaluations:'',// 电子化开评标
			amountId:''
		}
	},
	created(){
		this.getList();// 自行招标列表
	},
	methods:{
		//编号问题
        typeIndex(index) {
            return index + (this.currentPage - 1) * 10 + 1;
        },
		// 分页器
		handleSizeChange(val) {
			// 每页条数
		},
		handleCurrentChange(val) {
            // 当前页数
			this.currentPage = val;
			this.getList();// 自行招标列表
		},
		// 查看列表详情
		lookOver(row){
			this.entrySubcontractNumber = row.totalProjectId;
			this.showLookover = true;
			if(row.subcontractItePrimaryKeyId == null){
				this.getbiddingInfo(row.projectInformaPrimaryKeyId) // 招标项目信息
			}else {
				this.getbiddingInfo(row.projectInformaPrimaryKeyId) // 招标项目信息 
				this.getInfo(row.subcontractItePrimaryKeyId) // 分包信息
			}
			this.biddingProjectNumber = row.biddingProjectNumber;
			this.nameOfTenderProject = row.nameOfTenderProject;
			this.subcontractNameaa = row.subcontractName;
		},
		
		closeLook(){
			this.showLookover= false;
		},
		// 搜索
		searchList(){
			let obj = {};
			if(this.role_types == "招标(采购)人"){
				obj = {
					nameOfTenderProject:this.nameItem,
					totalProjectId:this.numberItem,
					pageNo:this.currentPage,
					pageSize:this.pageSize,
					procurementModes:["公开招标",'邀请招标'],
					biddingOrganization:'自行招标'
				}
			}else{
				obj = {
					nameOfTenderProject:this.nameItem,
					totalProjectId:this.numberItem,
					pageNo:this.currentPage,
					pageSize:this.pageSize,
					procurementModes:["公开招标",'邀请招标'],
					biddingOrganization:'委托招标'
				}
			}
			this.$axios({
				method: "POST",
				url: baseUrl+"/projectInformationController/selfTenderList",
				data:obj
			}).then(res=>{
				if(res.data.status == 200){
					this.biddingData = res.data.data.list;
					this.pages = res.data.data.pages;
					this.total = res.data.data.total;// 总条数
				}else{
					this.$message.warning(res.data.msg);
				}
			}).catch(err=>{
				console.log(err)
			})
		},
		// 操作 跳转详情页面
		handle(row){
			let obj = {
				projectInformaPrimaryKeyId:row.projectInformaPrimaryKeyId,
				totalProjectId:row.totalProjectId,
				subcontractName:row.subcontractName,
				subcontractItePrimaryKeyId:row.subcontractItePrimaryKeyId
			}
			window.localStorage.projectObj = JSON.stringify(obj);
			window.sessionStorage.setItem('str_entrust_type','委托角色');
			this.$router.push({
				name:'selfTenderDetail'
			}); 
		},
		// 自行招标列表
		getList(){
			let obj = {};
			if(this.role_types == "招标(采购)人"){
				obj = {
					nameOfTenderProject:this.nameItem,
					totalProjectId:this.numberItem,
					pageNo:this.currentPage,
					pageSize:this.pageSize,
					procurementModes:["公开招标",'邀请招标'],
					biddingOrganization:'自行招标'
				}
			}else{
				obj = {
					nameOfTenderProject:this.nameItem,
					totalProjectId:this.numberItem,
					pageNo:this.currentPage,
					pageSize:this.pageSize,
					procurementModes:["公开招标",'邀请招标'],
					biddingOrganization:'委托招标'
				}
			}
			this.$axios({
				method: "POST",
				url: baseUrl+"/projectInformationController/selfTenderList",
				data:obj
			}).then(res=>{
				if(res.data.status == 200){
					this.biddingData = res.data.data.list;// 自行招标列表
					this.pages = res.data.data.pages;
					this.total = res.data.data.total;// 总条数
				}else{
					this.$message.warning(res.data.msg);
				}
			}).catch(err=>{
				console.log(err)
			})
		},
		// 招标项目信息
        getbiddingInfo(projectInformaPrimaryKeyId){
            this.$axios({
				method: "GET",
				url: baseUrl+"/projectInformationController/selectById/"+projectInformaPrimaryKeyId,
			}).then(res=>{
				if(res.data.status == 200){
                    this.amountOfBiddingProject = res.data.data.amountOfBiddingProject;// 招标项目金额
                    this.typesOfBiddingProjects = res.data.data.typesOfBiddingProjects;// 招标项目类型
                    this.biddingProcurementMode = res.data.data.biddingProcurementMode;// 招标采购方式
                    this.biddingOrganization = res.data.data.biddingOrganization;//  招标组织方式
					this.tenderPurchaserName = res.data.data.tenderPurchaserName;// 招标(采购)人
					this.nameOfTenderProject =  res.data.data.nameOfTenderProject;// 招标项目名称
					this.biddingProjectNumber = res.data.data.biddingProjectNumber;// 招标项目编号
					this.qualificationExaminationMethods =  res.data.data.qualificationExaminationMethod;// 资格审查方式
					this.electronicBidEvaluations =  res.data.data.electronicBidEvaluation;// 是否电子招投标
					this.budgetAmount = res.data.data.budgetAmount;//预算金额
					this.tenderContents  = res.data.data.tenderContents ;//内容
					this.amountId = res.data.data.totalProjectId;// 入场项目编号
					this.subcontractName = res.data.data.nameOfTenderProject;// 名称
					this.biddingPurchasingAgencyName = res.data.data.biddingPurchasingAgencyName;
				}else{
					this.$message.warning(res.data.msg);
				}
			}).catch(err=>{
				console.log(err)
			})
		},
		// 分包信息
        getInfo(subcontractItePrimaryKeyId){
           this.$axios({
				method: "GET",
				url: baseUrl+"/subcontractItemController/selectById"+"?id"+"="+subcontractItePrimaryKeyId,
			}).then(res=>{
				if(res.data.status == 200){
                   this.entrySubcontractNumber =  res.data.data.entrySubcontractNumber;
                   this.subcontractName =  res.data.data.subcontractName;
                   this.subcontractContents =  res.data.data.subcontractContents;
                   this.scopeOfTender =  res.data.data.scopeOfTender;
                   this.subcontractingControlAmount =  res.data.data.subcontractingControlAmount;
                   this.qualificationExaminationMethod =  res.data.data.qualificationExaminationMethod;// 资格审查方式
                   this.electronicBidEvaluation = res.data.data.electronicBidEvaluation;// 电子化开评标
                }
			}).catch(err=>{
				console.log(err)
			})
        },
	}
}
</script>
<style scoped>
	.newBox {
		width: 100%;
		height: 100%;
		background: #fff;
    	position: relative;
	}
	.wrapper{
		width: 100%;
		height: 100%;
		display: flex;
		flex-direction: column;
	}
	.detailWrapper{
		 width: 100%;
		height: 100%;
		position: fixed;
		left: 0;
		top: 0;
		background: rgba(0, 0, 0, .3);
		z-index: 999;
		padding: 10px;
		display: flex;
		flex-direction: column;
	}
	.detailBox{
		width: 100%;
		height: 100%;
		display: flex;
		flex-direction: column;
		background: #fff;
	}
	.detail_title{
		width: 100%;
		height: 35px;
		padding: 0 20px;
		background: #0058a9;
		display: flex;
		justify-content: space-between;
		align-items: center;
		color: #fff;
		font-size: 18px;
	}
	.main_info{
		width: 100%;
		flex: 1;
		overflow: hidden;
		overflow-y: scroll;
	}
	.el-collapse{
		width: 80%;
		margin: 10px auto;
	}
	.el-collapse-item__content{
		padding-bottom: 0 !important;
	}
	/* 输入搜索框 */
	.pick_input{
		width: 100%;
		padding: 0 30px;
		height: 50px;
		display: flex;
		justify-content: space-between;
		align-items: center;
	}
	.noteItem{
		display: flex;
		align-items: center;
	}
	.noteItem p{
		padding:  0 5px;
	}
	.noteLeft{
		width: 130px;
		display: flex;
		justify-content: flex-end;
	}
	.write_input{
		width: 250px;
	}
	.write_input>.el-select{
		width: 250px;
	}
	.owner_btn{
		height: 35px;
		padding:0 25px;
		background-color: #0095d5;
		border: none;
		color: #ffffff;
		font-size: 16px;
		cursor: pointer;
		margin-right: 2px;
	}
	.btn{
		height: 35px;
		padding:0 25px;
		background-color: #0095d5;
		border: none;
		color: #ffffff;
		font-size: 16px;
		cursor: pointer;
		margin-right: 2px;
	}
	.pagination {
		width: 100%;
		padding: 20px 60px ;
		display: flex;
		align-items: center;
		justify-content: flex-end;
	}
	/* 列表展示 */
	.edit_list{
		padding: 15px 20px;
	}
	.editItem{
		width: 100%;
		display: flex;
		align-items: center;
	}
	.editCenter{
		flex: 1;
		display: flex;
		align-items: center;
		padding: 5px 0;
	}
	.editName{
		width: 145px;
		display: flex;
		justify-content: flex-end;
	}
	.editContent{
		flex: 1;
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 0 10px;
		color: #6e622e;
	}
	.editContent>p{
		width: 25%;
	}
	/* 文本框的样式 */
	.editTextarea{
		width: 100%;
		padding: 5px 0;
		display: flex;
	}
	.editBtn{
		height: 35px;
	}
</style>