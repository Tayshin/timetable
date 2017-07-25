<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-date"></i> 排课 </el-breadcrumb-item>
                <el-breadcrumb-item>排课算法</el-breadcrumb-item>
            </el-breadcrumb>
        </div>

        <div class="form-box">
            <el-form :inline="true" ref="postMsg" :model="postMsg" label-width="100px">
     
                <el-form-item label="年级">
                    <el-select @change="getCourse" v-model="postMsg.grade.id" placeholder="请选择">
                        <el-option v-for = "item in grades"
                                    :value = "item.id"
                                    :label="item.gradeName"
                                    ></el-option>
                    </el-select>
                </el-form-item>

                <el-form-item>
                    <el-button type="primary" @click="onSubmit('form')">创建</el-button>
                </el-form-item>

                <br>
                <el-form-item  label="config" >
                 <el-radio-group v-model="radio" @change = "changeRadio" >
                    <el-radio  class="radio" label='true'>单双周课表</el-radio>
                    <el-radio  class="radio"  label='false'>普通课表</el-radio>
                 </el-radio-group>
                </el-form-item>
                <br>

                <el-form-item v-for="course in courses" label = "courseDetail">
                    <el-input label="courseName"  :disabled="true" v-model ="course.courseName" sortable></el-input>
                    <el-input type = "number" :min = "minNum" :max = "maxNum" label="courseNum" v-model="course.courseNum" placeholder="input the num of course"></el-input>
                </el-form-item>
                <br>
                <el-form-item label-width="100px" label=" ">
                    <el-button type="warning" @click="onSubmit">Submit</el-button>
                </el-form-item>
                </el-form>
        </div>

    </div>
</template>


<script>
 export default {
    data(){
        return{
            minNum:0,
            maxNum:10,
            //first : get all grades, data is a array with[gradeId]
            getGradeUrl:"http://110.64.72.31:8888/timetable/algo/getAllGrade",
            getCourseUrl:"/algo/getAllCourse",
            postMsg:{
                doubleWeek:true,
                grade:{
                    id:'',
                    gradeName:'grade1',
                    numClassPerDay:1,
                    numClass:1
                },
                gradeCourse:[
                //     {
                //     id:1,
                //     groupId:1,
                //     courseId:1,
                //     courseNum:''
                // }
                ]
            },
            radio: 'false',
            grades:[
                
                
            ],
            courses:[
                    {
                    id:1,
                    groupId:1,
                    courseName:'yuwen',
                    courseNum:0
                },
                {
                    id:2,
                    groupId:2,
                    courseName:'shuxue',
                    courseNum:0
                },
                {
                    id:3,
                    groupId:3,
                    courseName:'yingyu',
                    courseNum:0
                }
                ]
            
        }
    },
    methods:{
        changeRadio(){
            var vm = this;
            if(vm.radio == 'true')
            vm.postMsg.doubleWeek = true;
            else
            vm.postMsg.doubleWeek = false;
            console.log(vm.radio);
            console.log(vm.postMsg.doubleWeek);
        },

        onSubmit(){
            var self = this;
            for(let i = 0;i<self.grades.length;i++){
                if(self.postMsg.grade.id == self.grades[i].id){
                    self.postMsg.grade.gradeName = self.grades[i].gradeName;
                    self.postMsg.grade.numClassPerDay = self.grades[i].numClassPerDay;
                    self.postMsg.grade.numClass = self.grades[i].numClass;
                }
            }
            var k = 0;
            for(let j =0;j<self.courses.length;j++){   
                if(parseInt(self.courses[j].courseNum) != 0){
                    self.postMsg.gradeCourse.push({
                        id:1,
                        groupId:0,
                        courseId:0,
                        courseNum:0,
                    });
                    self.postMsg.gradeCourse[k].groupId = self.courses[j].groupId;
                    self.postMsg.gradeCourse[k].courseId = self.courses[j].id;
                    self.postMsg.gradeCourse[k].courseNum = self.courses[j].courseNum;
                    k++;
                }
            }

            console.log(self.postMsg);
        },
        getGrade(){
            var vm = this;
            $.ajax({
                url:vm.getGradeUrl,
                type:'GET',
                contentType:'json',
                dataType:'json',
                success:function(data){
                    console.log("ajax 1 success")
                    for(let i = 0;i<data.length;i++){
                        vm.grades.push({
                            id:1,
                            gradeName:'grade 1',
                            numClassPerDay:1,
                            numClass:1                     
                        });
                        vm.grades[i].id = parseInt(data[i].id);
                        vm.grades[i].gradeName = data[i].gradeName;
                        vm.grades[i].numClassPerDay = parseInt(data[i].numClassPerDay);
                        vm.grades[i].numClass = parseInt(data[i].numClass);
                    }
                },
                error:function(error){
                    console.log(error);
                }
            });
        },
        getCourse(){
            this.$axios({
                    url: vm.getCourseUrl + "/" + vm.postMsg.grade.id,
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
                    console.log("ajax2 success");
                    this.courses = response.data;
                })
                .catch((error) => {
                    console.log("Error:", error);
                    console.log("get courses fail")
                });

                this.$message.success('提交成功！');

            // var vm = this;
            // $.ajax({
            //     url:vm.getCourseUrl + "/" + vm.postMsg.grade.id,
            //     // url:vm.getCourseUrl,
            //     type:'POST',
            //     contentType:'json',
            //     dataType:'json',
            //     success:function(data){
            //         console.log("ajax2 success");
            //         vm.courses = data;
            //     },
            //     error:function(error){
            //         console.log("get courses fail")
            //     }
            // });
        }
    },
    mounted(){
        this.getGrade();
    }

  }
</script>
