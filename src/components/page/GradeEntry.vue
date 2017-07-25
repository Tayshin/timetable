<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-date"></i> 年级</el-breadcrumb-item>
                <el-breadcrumb-item>年级信息录入</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="form-box">
            <el-form :inline="true" ref="gradeInfo" :model="gradeInfo" label-width="100px">
     
                <el-form-item label="年级">
                    <el-select v-model="gradeInfo.gradeName" placeholder="请选择">
                        <el-option v-for = "item in options"
                                    :value = "item.key"
                                    :label="item.label"
                                    ></el-option>
                    </el-select>
                </el-form-item>
                <!-- <el-form-item>
                    <el-input
                      placeholder="自定义年级"
                      v-model="gradeInfo.elseOption"
                      :disabled="elseOptionState">
                    </el-input>
                </el-form-item> -->
                <el-form-item label="班级数" prop="grade">
                    <el-input type = "number" :min = "minNum" :max = "maxNum" v-model="gradeInfo.numClass"></el-input>
                </el-form-item>   
                <el-form-item label="每天上课节数" prop="grade">
                    <el-input type = "number" :min = "minNum" :max = "maxNum" v-model="gradeInfo.numClassPerDay"></el-input>
                </el-form-item>  
                <el-form-item>
                    <el-button type="primary" @click="onSubmit('form')">创建</el-button>
                    <el-button @click="update()">刷新列表</el-button>
                </el-form-item>
            </el-form>
        </div>
        
        <el-table :data="tableData" height stripe style="width: 60%">
            <el-table-column prop="id" label="ID" sortable width="250">
            </el-table-column>
            <el-table-column prop="gradeName" label="年级" sortable>
            </el-table-column>
             <el-table-column prop="numClassPerDay" label="每天上课节数" sortable>
            </el-table-column>
            <el-table-column prop="numClass" label="班级数" sortable>
                
            </el-table-column>


            <el-table-column label="操作">
            <template scope="scope">
            
                <el-button
                class="el-icoj-delete"
                size="small"
                type="danger"
                @click="handleDelete(scope.$index, scope.row)">删除</el-button>
            </template>
            </el-table-column>
        </el-table>
        
        <div class="pagination">
            <el-pagination
                    @current-change ="handleCurrentChange"
                    @size-change ="handleSizeChange"
                    layout="total, prev, pager, next"
                    v-bind:total="total">
            </el-pagination>
        </div>
    </div>
</template>


<script>
    export default {
        data() {
            return {
                minNum:1,
                maxNum:20,
                options: [{
                    value:'grade1',
                    key:'grade1',
                    label:'一年级'
                },
                {
                    value:'grade2',
                    key:'grade2',
                    label:'二年级'
                },
                {
                    value:'grade3',
                    key:'grade3',
                    label:'三年级'
                },
                {
                    value:'grade4',
                    key:'grade4',
                    label:'四年级'
                },
                {
                    value:'grade5',
                    key:'grade5',
                    label:'五年级'
                },
                {
                    value:'grade6',
                    key:'grade6',
                    label:'六年级'
                },
                {
                    value:'grade7',
                    key:'grade7',
                    label:'七年级' 
                },
                {
                    value:'grade8',
                    key:'grade8',
                    label:'八年级'
                },
                {
                    value:'grade9',
                    key:'grade9',
                    label:'九年级'   
                },
                {
                    value:'grade10',
                    key:'grade10',
                    label:'高一'
                },
                {
                    value:'grade11',
                    key:'grade11',
                    label:'高二'
                },
                {
                    value:'grade12',
                    key:'grade12',
                    label:'高三'
                }
                // {
                //     value:'grade13',
                //     key:'13',
                //     label:'其他'
                // }
                ],
                elseOptionState:true,
                tableData: [{
                    id:1,
                    gradeName:"1",
                    numClassPerDay:1,
                    numClass:2
                }
                ],
                targetData:{
                    id:'1'
                },

                cur_page: 1,
                total: "10",
                gradeInfo: {
                    gradeName: '',
                    // elseOption:'',
                    numClassPerDay:'',
                    numClass:''
                },
                postUrl:'http://110.64.72.31:8888/timetable/createGrade',
                getUrl:'http://110.64.72.31:8888/timetable/grade',
                deleteUrl:'http://110.64.72.31:8888/timetable/deleteGrade',
                total:0
            }
        },
        created() {
            self.total = self.tableData.length;
            this.getData();
        },
        methods: {
            handleDelete(index, row){
                console.log(row.id);
                var self = this;
                self.deleteUrl = "http://110.64.72.31:8888/timetable/deleteGrade"+"/"+row.id;
                console.log(self.targetData);
                $.ajax({
                    url:self.deleteUrl,
                    type:'GET',
                    success:function(){
                        self.getData();
                    },
                    error:function(error){
                        self.getData();
                    }
                });
                // var self = this;
                // console.log(row.id);

                // this.$axios({
                //     url: '/deleteGrade',
                //     method: 'post',
                //     baseURL: 'http://192.168.1.113:8888/timetable',
                //     data: JSON.stringfy(target);
                // })
                // .then((response) => {
                //     console.log(response);
                //     this.tableData = response.data;
                // })
                // .catch((error) => {
                //     console.log("Error:", error)
                // });

                // this.$message.success('提交成功！');
            },

            onSubmit(formName) {
                var self = this;
                console.log(this.gradeInfo)

                this.$axios({
                    url: '/createGrade',
                    method: 'post',
                    baseURL: 'http://110.64.72.31:8888/timetable',
                    data: self.gradeInfo
                })
                .then((response) => {
                    console.log(response);
                    this.tableData = response.data;
                })
                .catch((error) => {
                    console.log("Error:", error)
                });

                this.$message.success('提交成功！');
                // var self = this;
                // console.log(self.gradeInfo);
                // $.ajax({
                //     url:self.postUrl,
                //     type: 'POST',
                //     dataType: 'json',
                //     contentType:'application/json',
                //     data: self.gradeInfo,
                //     success: function(){
                //         alert("success!");
                //     },
                //     error: function(){
                //         alert("error");
                //     }
                // });

            },
            update() {
                console.log(this.gradeInfo.gradeName);
                // console.log('update');
                this.getData();
                // // this.$refs[formName].resetFields();
            },            
            handleCurrentChange(val){
                this.cur_page = val;
                this.getData();
            },
            handleSizeChange() {

            },
            getData() {
                // this.$axios({
                //     url: '/subject_group',
                //     method: 'get',
                //     baseURL: 'http://192.168.1.174:8888/timetable'
                // })
                // .then((response) => {
                //     // 需要使用arrow function来共享变量
                //     this.tableData = response.data;
                //     // this.total = this.tableData.length;
                // })
                // .catch((error) => {
                //     console.log("Error:", error)
                // });
                var self = this;
                $.ajax({
                    url:self.getUrl,
                    type:'GET',
                    contentType:'application/json',
                    dataType:'json',
                    success:function(data){
                        self.tableData = data;
                        self.total = self.tableData.length;

                    },
                    error:function(error){
                        console.log(error);
                    }

                });
            }
        }
    }
</script>