<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-date"></i> 课程</el-breadcrumb-item>
                <el-breadcrumb-item>科目组管理</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="form-box">
            <el-form :inline="true" ref="lesson" :model="lesson" label-width="100px">
                <el-form-item label="科目组名称" prop="name">
                    <el-input v-model="lesson.name"></el-input>
                </el-form-item>            
                <el-form-item>
                    <el-button type="primary" @click="onSubmit('form')">创建</el-button>
                    <el-button @click="update()">刷新列表</el-button>
                </el-form-item>
            </el-form>
        </div>
        
        <el-table :data="tableData" stripe max-height="400" style="width: 100%">
        <el-table-column prop="id" label="ID" sortable width="250">
        </el-table-column>
        <el-table-column prop="groupName" label="科目组名称" width="300">
        </el-table-column>
        <el-table-column label="操作" fixed="right" width="300">
            <template scope="scope">
                <el-button 
                    size="small"
                    icon="delete"
                    type="danger"
                    @click="handleDelete(scope.$index, scope.row)">
                    删除
                </el-button>
            </template>
        </el-table-column>
        </el-table>
    </div>
</template>


<script>
    export default {
        data() {
            return {
                tableData: [],
                lesson: {
                    name: ''
                }
            }
        },
        created() {
            this.getData();
        },
        methods: {
            onSubmit(formName) {
                // 使用this.lesson.name来访问变量
                this.$axios({
                    url: '/subject_group',
                    method: 'post',
                    // baseURL: 'http://192.168.43.35:8080/timetable',
                    // baseURL: 'http://192.168.1.174:8080/timetable',
                    baseURL: 'http://110.64.72.31:8888/timetable',
                    data: {
                        groupName: this.lesson.name
                    }
                })
                .then((response) => {
                    console.log(response);
                    this.tableData = response.data;
                })
                .catch((error) => {
                    console.log("Error:", error);
                });

                this.$message.success('提交成功！');

            },
            update() {
                console.log('update');
                this.getData();
                // this.$refs[formName].resetFields();
            },
            handleDelete(index, row) {
                console.log(index, row);
                console.log(row.id, row.groupName);
                this.$axios({
                    url: '/subject_group/' + row.id,
                    method: 'delete',
                    // baseURL: 'http://192.168.43.35:8080/timetable'
                    // baseURL: 'http://192.168.1.174:8080/timetable',
                    baseURL: 'http://110.64.72.31:8888/timetable'
                })
                .then((response) => {
                    console.log("Delete Success!")
                    this.tableData = response.data;
                })
                .catch((error) => {
                    console.log("Error:", error);
                });
            },
            getData() {
                this.$axios({
                    url: '/subject_group',
                    method: 'get',
                    // baseURL: 'http://192.168.43.35:8080/timetable'
                    // baseURL: 'http://192.168.1.174:8080/timetable',
                    baseURL: 'http://110.64.72.31:8888/timetable'
                })
                .then((response) => {
                    // 需要使用arrow function来共享变量
                    this.tableData = response.data;
                })
                .catch((error) => {
                    console.log("Error:", error);
                });
            }
        }
    }
</script>