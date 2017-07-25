<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-date"></i> 教师</el-breadcrumb-item>
                <el-breadcrumb-item>教师信息录入</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="form-box">
            <el-form ref="teacher" :model="teacher" label-width="100px">
                <el-form-item label="教师姓名" prop="name">
                    <el-input v-model="teacher.name"></el-input>
                </el-form-item>
                <el-form-item label="选择相关科组" prop="region" >
                    <el-select v-model="teacher.region" placeholder="请选择所在科目组">
                        <div v-for="item in teacher.selectList">
                            <el-option key="item.id" :value="item.id" :label="item.groupName">{{ item.groupName }}</el-option>
                        </div>
                    </el-select>
                </el-form-item>
                <el-form-item>
                    <el-button type="primary" @click="onSubmit">提交</el-button>
                    <el-button type="warning" @click="clear('teacher')">重置</el-button>
                </el-form-item>
            </el-form>
        </div>


        <el-table :data="tableData" stripe max-height="400" style="width: 100%">
        <el-table-column type="expand">
          <template scope="props">
            <el-form label-position="left" inline class="demo-table-expand">
                <div v-for="item in props">
                    <div v-for="teacher in item.teachers">
                      <el-form-item label="教师ID: ">
                        <span>{{ teacher.id }}</span>
                      </el-form-item>
                      <el-form-item label="教师姓名: ">
                        <span>{{ teacher.teacherName }}</span>
                      </el-form-item>
                      <el-form-item label="点击删除 ">
                        <el-button 
                            size="small"
                            icon="delete"
                            type="danger"
                            @click="handleDelete(teacher.id)">
                            删除
                        </el-button>
                      </el-form-item>
                    </div>
                </div>
            </el-form>
          </template>
        </el-table-column>
        <el-table-column prop="id" label="科组ID" width="300" sortable>
        </el-table-column>
        <el-table-column prop="groupName" label="科组名称" width="300">
        </el-table-column>
        <el-table-column prop="num" label="科组人数" width="300" sortable>
        </el-table-column>
        </el-table>


    </div>
</template>
<style>
  .demo-table-expand {
    font-size: 0;
    margin-left: 10px;
  }
  .demo-table-expand label {
    color: #99a9bf;
  }
  .demo-table-expand .el-form-item {
    margin-right: 0;
    margin-bottom: 0;
    width: 300px;
  }
</style>

<script>
    export default {
        data() {
            return {
                tableData: [],
                teacher: {
                    name: '',
                    region: '',
                    selectList: []
                }
            }
        },
        created() {
            // 获取科目组列表
            this.$axios({
                url: '/subject_group',
                method: 'get',
                // baseURL: 'http://192.168.43.35:8080/timetable'
                // baseURL: 'http://192.168.1.174:8080/timetable',
                baseURL: 'http://110.64.72.31:8888/timetable'   
            })
            .then((response) => {
                this.teacher.selectList = response.data;
            })
            .catch((error) => {
                console.log("Error:", error);
            });

            // 获取老师列表
            this.$axios({
                url: '/teacher',
                method: 'get',
                // baseURL: 'http://192.168.43.35:8080/timetable'
                // baseURL: 'http://192.168.1.174:8080/timetable',
                baseURL: 'http://110.64.72.31:8888/timetable'
            })
            .then((response) => {
                this.tableData = response.data;
            })
            .catch((error) => {
                console.log("Error:", error);
            });
        },
        methods: {
            onSubmit() {
                this.$message.success('提交成功！' + this.teacher.region);
                this.$axios({
                    url: '/teacher',
                    method: 'post',
                    // baseURL: 'http://192.168.43.35:8080/timetable'
                    // baseURL: 'http://192.168.1.174:8080/timetable',
                    baseURL: 'http://110.64.72.31:8888/timetable',
                    data: {
                        teacherName: this.teacher.name,
                        groupId: this.teacher.region
                    }
                })
                .then((response) => {
                    this.tableData = response.data;
                })
                .catch((error) => {
                    console.log("Error:", error);
                });
            },
            clear(formElement) {
                this.$refs[formElement].resetFields();
            },
            handleDelete(teacherID) {
                console.log(teacherID);
                this.$axios({
                    url: '/teacher/' + teacherID,
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
        }
    }
</script>