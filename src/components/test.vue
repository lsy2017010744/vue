<template>
    <div>
        <el-container>
            <el-header>
                <el-tabs v-model="activeName" @tab-click="handleClick">
                    <el-tab-pane label="待完成" name="first">
                        <el-button type="primary" size = "mini" @click="Add_showdialog">添加任务</el-button>
                        <el-col>
                        </el-col>

                        <!--待完成表格-->
                        <el-table :data="taskTest" style="width: 100%">
                            <el-table-column label="序号" width="180">
                                <template slot-scope="scope">
                                    <span>{{ scope.$index + 1 }}</span>
                                </template>
                            </el-table-column>
                            <el-table-column label="任务" width="180">
                                <template slot-scope="scope">
                                    <span>{{scope.row.task_name}}</span>
                                </template>
                            </el-table-column>
                            <el-table-column label="任务等级">
                                <template slot-scope="scope">
                                    <span>{{scope.row.task_level | replace }}</span>
                                </template>
                            </el-table-column>
                            <el-table-column label="开始时间" width="180">
                                <template slot-scope="scope">
                                    <span>{{scope.row.start_time | dateFormat}}</span>
                                </template>
                            </el-table-column>
                            <el-table-column label="结束时间">
                                <template slot-scope="scope">
                                    <span>{{scope.row.end_time | dateFormat}}</span>
                                </template>
                            </el-table-column>

                            <el-table-column label="完成进度">
                                <template slot-scope="scope">
                                    <span>{{scope.row.completion_status }}</span>
                                </template>
                            </el-table-column>
                            <el-table-column prop="options" label="操作">
                                <template slot-scope="scope">
                                    <el-button type="text" size="small"
                                        @click="Edit(scope.$index,scope.row)">编辑</el-button>
                                    <el-button type="text" size="small" @click="finishTask(scope.$index,scope.row)">完成
                                    </el-button>
                                    <el-button type="text" size="small" @click="Task(scope.$index,scope.row)">暂时不需要
                                    </el-button>
                                    <el-button type="text" size="small" @click="deleteTask(scope.$index,scope.row)">删除
                                    </el-button>
                                </template>
                            </el-table-column>
                        </el-table>
                    </el-tab-pane>
                    <!--已完成表格-->
                    <el-tab-pane label="已完成" name="second">
                        <el-table :data="finishTaskList" style="width: 100%">
                            <el-table-column label="序号" width="180">
                                <template slot-scope="scope">
                                    <span>{{ scope.$index + 1 }}</span>
                                </template>
                            </el-table-column>
                            <el-table-column label="任务名称" width="180">
                                <template slot-scope="scope">
                                    <span>{{scope.row.task_name}}</span>
                                </template>
                            </el-table-column>
                            <el-table-column label="开始时间" width="180">
                                <template slot-scope="scope">
                                    <span>{{scope.row.start_time | dateFormat}}</span>
                                </template>
                            </el-table-column>
                            <el-table-column label="结束时间">
                                <template slot-scope="scope">
                                    <span>{{scope.row.end_time | dateFormat}}</span>
                                </template>
                            </el-table-column>
                            <el-table-column label="完成时间">
                                <template slot-scope="scope">
                                    <span>{{scope.row.finished_time | dateFormat}}</span>
                                </template>
                            </el-table-column>

                            <el-table-column prop="options" label="操作">
                                <template slot-scope="scope">
                                    <el-button type="text" size="small"
                                        @click="restoreFromFinishTaskList(scope.$index,scope.row)">还原</el-button>

                                    <el-button type="text" size="small"
                                        @click="deletedFromFinishTaskList(scope.$index,scope.row)">删除</el-button>
                                </template>
                            </el-table-column>
                        </el-table>
                    </el-tab-pane>
                       <!-- 已删除表格 -->
                    <el-tab-pane label="已删除" name="third">
                  
                 <el-table :data="deleted" style="width: 100%">
                     <el-table-column label="序号" width="180">
                         <template slot-scope="scope">
                             <span>{{ scope.$index + 1 }}</span>
                         </template>
                     </el-table-column>
                     <el-table-column label="任务名称" width="180">
                         <template slot-scope="scope">
                             <span>{{scope.row.task_name}}</span>
                         </template>
                     </el-table-column>
                     <el-table-column label="开始时间" width="180">
                         <template slot-scope="scope">
                             <span>{{scope.row.start_time | dateFormat}}</span>
                         </template>
                     </el-table-column>
                     <el-table-column label="结束时间">
                         <template slot-scope="scope">
                             <span>{{scope.row.end_time | dateFormat}}</span>
                         </template>
                     </el-table-column>
                     <el-table-column label="删除时间">
                         <template slot-scope="scope">
                             <span>{{scope.row.deleted_time | dateFormat}}</span>
                         </template>
                     </el-table-column>
                    
                     <el-table-column label="完成情况">
                         <template slot-scope="scope">
                             <span>{{scope.row.completion_status }}</span>
                         </template>
                     </el-table-column>
                     <el-table-column prop="options" label="具体操作">
                         <template slot-scope="scope">
                             <el-button type="text" size="small"
                                 @click="reductionFromDeletedToUnfinish(scope.$index,scope.row)">
                                 还原
                             </el-button>

                             <el-button type="text" size="small"
                                 @click="deletedFromTaskList(scope.$index,scope.row)">彻底删除</el-button>
                         </template>
                     </el-table-column>
                 </el-table>
             </el-tab-pane>

                
                </el-tabs>


            </el-header>
            
        </el-container>
        
        <!--弹窗-->
        <el-dialog title="任务" :visible.sync="dialogFormVisible">
            <el-form :model="addForm">
                <el-form-item label="任务" :label-width="formLabelWidth">
                    <el-input v-model="addForm.task_name" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="任务等级:" :label-width="formLabelWidth">
                    <div align="left">
                        <el-select v-model="addForm.task_level " placeholder="请选择任务等级">
                            <el-option label="Ordinary" value=0></el-option>
                            <el-option label="Important" value=1></el-option>
                            <el-option label="Emergency" value=2></el-option>
                        </el-select>
                    </div>
                </el-form-item>
                <div align="left">
                    <div>
                        <span class="demonstration">请选择开始日期:</span>
                        <div class="demonstration"></div>
                        <el-date-picker v-model="addForm.start_time" type="date" placeholder="选择日期"
                            format="yyyy 年 MM 月 dd 日">
                        </el-date-picker>
                    </div>
                    <div style="margin-top:20px;">
                        <span class="demonstration">请选择结束日期:</span>
                        <div class="demonstration"></div>
                        <el-date-picker v-model="addForm.end_time" type="date" placeholder="选择日期"
                            format="yyyy 年 MM 月 dd 日">
                        </el-date-picker>
                    </div>
                </div>
                <div class="block" style="margin-top:20px;">
                    <span class="demonstration">任务完成情况</span>
                    <el-slider v-model="addForm.completion_status"
                    show-input=""></el-slider>
                </div>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="cancelTask">取 消</el-button>
                <el-button type="primary" @click="addTask">确 定</el-button>
            </div>
        </el-dialog>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                alltime:"2000-00-00",
                activeName: 'first',
                dialogFormVisible: false,
                editIndex: 0,
                addForm: {
                    task_name: "",
                    task_details: "",
                    start_time: null,
                    end_time: null,
                    deleted_time: null,
                    finished_time: null,
                    task_level: 0,
                    completion_status: 0
                },
                //未完成
                taskTest: [{
                        task_name: "123",
                        task_level: "1",
                        start_time: "2018-01-01",
                        end_time: "2019-01-01",
                        completion_status: 50,


                    },
                    {
                        task_name: "456",
                        task_level: "0",
                        start_time: "2018-02-01",
                        end_time: "2019-02-10",
                        completion_status: 40,


                    }
                ],
                //已完成
                finishTaskList: [{
                    task_name: "789",
                     task_level: "0",
                     completion_status: 100,
                    start_time: "2017-03-28",
                    end_time: "2017-04-28",
                    finished_time: "2017-03-30"

                }],
                //已删除
                 deleted: [{
                     task_name: "abc",
                    
                     start_time:"2017-07-07",
                     end_time: "2017-07-08",
                     deleted_time: "2017-07-09",
                     finished_time: null,
                     task_level: 1,
                     completion_status: 50
                 }],
            };
        },
        methods: {
            //完成任务
             finishTask(index, item) {
                 item.completion_status = 100
                 //设置完成时间为当前操作时间
                 item.finished_time = new Date()
                 //添加到已完成列表中去
                 this.finishTaskList.push(JSON.parse(JSON.stringify(item)))
                 //从未完成中删除
                 this.taskTest.splice(index, 1)

             },
             Task(index, item) {
                 //设置完成时间为当前操作时间
                 item.finished_time = new Date()
                 //添加到已完成列表中去
                 this.finishTaskList.push(JSON.parse(JSON.stringify(item)))
                 //从未完成中删除
                 this.taskTest.splice(index, 1)

             },
              //从已完成列表中还原到未完成列表
             restoreFromFinishTaskList(index, item) {
                 //将完成时间设为空
                 item.finished_time = null
                 //从已完成列表中删除
                 this.finishTaskList.splice(index, 1)
                 //重新添加到未完成列表
                 this.taskTest.push(JSON.parse(JSON.stringify(item)))

             },
           
             //从已完成列表中删除,到垃圾桶中（已删除）
             deletedFromFinishTaskList(index, item) {
                 //设置删除时间
                 item.deleted_time = new Date()
                 //添加到已删除集合
                 this.deleted.push(JSON.parse(JSON.stringify(item)))
                 //从已完成集合中删除
                 this.finishTaskList.splice(index, 1)
             },
             //从已删除列表中还原到未完成列表
             reductionFromDeletedToUnfinish(index, item) {
                 //清空删除时间
                 item.deleted_time = null
                 //把当前item添加到未完成列表
                 if(item.completion_status == 100)
                 {
                     this.finishTaskList.push(JSON.parse(JSON.stringify(item)))
                 }    
                 else if(item.completion_status !=100){
                 this.taskTest.push(JSON.parse(JSON.stringify(item)))
                 }
                 //从已删除列表移除
                 this.deleted.splice(index, 1)

             },
           
            //彻底删除
            deletedFromTaskList(index,item){
                this.item = null
                this.deleted.splice(index,1)
            },
            Add_showdialog() {
                 //清空addForm
                 this.addForm.task_name = ""
                 this.addForm.task_details = ""
                 this.addForm.start_time = null
                 this.addForm.end_time = null
                 this.addForm.deleted_time = null
                 this.addForm.finished_time = null
                 this.addForm.task_level = 0
                 this.addForm.completion_status = 0
                 //添加任务对话框可见
                 this.dialogFormVisible = true
                 //现在进行的是添加操作
                 this.isAdd = true
             },
             //编辑对话框
             Edit(index, item) {
                 //添加任务对话框可见
                 this.dialogFormVisible = true
                 this.isAdd = false
                 this.editIndex = index
                 //将传入的对象渲染表单对象
                 this.addForm = JSON.parse(JSON.stringify(item))

             },
             addTask() {
                 //判断是否为空，是否是添加需求,添加则是push
                 if (this.isAdd) {
                     var resmble = JSON.parse(JSON.stringify(this.addForm))
                     this.taskTest.push(resmble)
                     this.isAdd = false
                 } else if (!this.isAdd) {
                     //否则是splice
                     //进行替换
                     this.taskTest.splice(this.editIndex, 1, JSON.parse(JSON.stringify(this.addForm)))
                     this.editIndex = 0
                 }
                 //清空addForm
                 this.addForm.task_name = ""
                 this.addForm.task_details = ""
                 this.addForm.start_time = null
                 this.addForm.end_time = null
                 this.addForm.deleted_time = null
                 this.addForm.finished_time = null
                 this.addForm.task_level = 0
                 this.addForm.completion_status = 0
                 // this.addForm = null
                 this.dialogFormVisible = false
                 // this.addForm = null

             },
              deleteTask(index, item) {
            
                 //把任务从未完成列表删除
                 this.taskTest.splice(index, 1)
                 //设定删除时间等于现在操作时间
                 item.deleted_time = new Date()
                 //将当前任务添加进已删除列表
                 this.deleted.push(JSON.parse(JSON.stringify(item)))

                 //表单清空
                 this.addForm.task_name = ""
                 this.addForm.task_details = ""
                 this.addForm.start_time = null
                 this.addForm.end_time = null
                 this.addForm.deleted_time = null
                 this.addForm.finished_time = null
                 this.addForm.task_level = 0
                 this.addForm.completion_status = 0
                 // this.addForm = null
                 //表格隐藏
                 this.dialogFormVisible = false
             },
             //取消按钮
             cancelTask() {
                 // this.addForm = null

                 this.dialogFormVisible = false
             }
        },
        filters: {
            dateFormat: function (datestr) {
                 var dt = new Date(datestr)

                 var year = dt.getFullYear()
                 var month = dt.getMonth() + 1
                 var day = dt.getDate()

                 return `${year}-${month}-${day}`
             },
            replace: function (num) {
                if (num == 0 || num == '0') {
                    return 'Ordinary'
                }
                if (num == 1 || num == '1') {
                    return 'Important'
                }
                if (num == 2 || num == '2') {
                    return 'Emergency'
                }
            }
        }
    };
</script>
<style>
    .el-header,
    .el-footer {
        background-color: #B3C0D1;
        color: #333;
        text-align: center;
        line-height: 60px;
    }



    .el-main {
        background-color: #E9EEF3;
        color: #333;
        text-align: center;
        line-height: 560px;
    }
</style>