<template>
    <div class="wrapper">

        <div class="search">
            <div>
                <el-button type="primary" @click="addMedia"><i class="el-icon-edit" />创建拼团</el-button>
            </div>
        </div>

        <el-table :border="true" :data="list"> 
            <el-table-column label="拼团内容" class="media">
                <template slot-scope="scope" >
                    <div class="media-left">
                        <img :src="scope.row.value.cover" alt="" style="width: 100px">
                    </div>
                    <div class="media-right">
                        <p>{{ scope.row.value.title }}</p>
                        <p >原始价格: <span style="color:red">￥{{ scope.row.price }}</span></p>
                        <p >拼团价格: <span style="color:red">￥{{ scope.row.value.price }}</span></p>
                    </div>
                </template>
            </el-table-column>
            <el-table-column label="成团人数" align="center" width="150">  
                <template slot-scope="scope">
                    {{ scope.row.p_num }}
                </template>
            </el-table-column>
              <el-table-column label="拼团时限(小时)" align="center" width="200">
                <template slot-scope="scope">
                    {{ scope.row.expire }}
                </template>
            </el-table-column>
            <el-table-column label="拼团状态" align="center" width="150">  
                <template slot-scope="scope">
                   <span :style="{color: scope.row.time_status === '拼团中' ? 'red' : '#d4b8b8' }">{{ scope.row.time_status }}</span> 
                </template>
            </el-table-column>
          
            <el-table-column label="操作" align="center" width="300">
                <template slot-scope="scope">
                    <el-button size="mini" type="primary" @click="editData(scope.$index, scope.row)">编辑</el-button>
                    <el-button size="mini" @click="changeStatus(scope.$index, scope.row)" :disabled="scope.row.time_status === '已下架'" type="danger">下架</el-button>
                  
                </template>
            </el-table-column>
        </el-table>

        <!-- 编辑与添加 -->
       <el-dialog :title="isEdit ?  '编辑':'新增'" :visible.sync="dialogFormVisible" >
            <el-form ref="form" :model="form" label-width="150px" :rules="rules">
                <el-form-item label="类型" prop="type">
                    <el-input v-model="form.type" style="width: 30%"></el-input>
                </el-form-item>
                <el-form-item label="关联课程" prop="title">
                    <el-button type="primary">关联</el-button>
                </el-form-item>
                <el-form-item label="拼团价" style="width: 50%">
                    <el-input-number v-model="form.value.price" :min="0"></el-input-number>
                </el-form-item>
                <el-form-item label="拼团人数"  style="width: 50%" prop="content">
                    <el-input-number v-model="form.p_num" :min="0"></el-input-number>
                </el-form-item>
                <el-form-item label="是否开始凑团" style="width: 50%" prop="try">
                    <el-radio-group v-model="radio">
                        <el-radio :label="3">备选项</el-radio>
                        <el-radio :label="6">备选项</el-radio>
                        <el-radio :label="9">备选项</el-radio>
                    </el-radio-group>
                </el-form-item>
                <el-form-item label="拼团时限" style="width: 50%" prop="try">
                    <el-radio-group v-model="form.expire">
                        <el-radio :label="24">24小时</el-radio>
                        <el-radio :label="48">48小时</el-radio>
                    </el-radio-group>
                </el-form-item>
                <el-form-item label="活动开始/结束时间" >
                   <el-input-number v-model="form.price" :step="1" :min='0'></el-input-number>
                </el-form-item>

                <el-form-item>
                    <el-button type="primary" @click="onSubmit('form')">提交</el-button>
                    <el-button>取消</el-button>
                </el-form-item>
            </el-form>
        </el-dialog>


        <div class="block">
            <el-pagination
                background
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="list.page"
                :page-sizes="[20, 30, 40, 50]"
                :page-size="list.limit"
                layout="total, sizes, prev, pager, next, jumper"
                :total="total"
            >
            </el-pagination>
        </div>
    </div> 
</template>

<script>

import { fetchList, updateAudio } from '../../api/audio'
import { fetchGroup } from '../../api/marketing'
import Singlemage from '../../components/Upload/SingleImage'
import Tinymce from '@/components/Tinymce'

const listQuery = {
    page:1,
    limit: 20,
}

const formDefault = {
    auto: 1,
    created_time: "",
    end_time: "",
    expire: null,
    id: null,
    p_num: null,
    price: null,
    start_time: "",
    status: 0,
    time_status: "",
    type: "",
    updated_time: "",
    value:{
        cover: "http://dummyimage.com/200x100",
        id: 84602,
        price: 10,
        title: "人文至会现比。",
    }
   
}

export default {
    components:{
        Singlemage,
        Tinymce,
    },
    data () {
        return {
            value:null,
            total:null,
            list: [],
            listQuery: Object.assign({}, listQuery),
            dialogFormVisible: false,
            formLabelWidth: '80',
            form: Object.assign({}, formDefault),
            rules:
            {
                title:[
                    {required: true, message: "请输入标题", trigger: 'blur'}
                ],
                try: [
                    {required: true, message: "请输入试看内容", trigger: 'blur'}
                ],
                content:[
                    {required: true, message: "请输入课程内容", trigger: 'blur'}
                ]
            },
            isEdit: false,
            visible: false,
            currentindex:null,

        }
    },

    mounted () {
        this.getMedia()
    },

    methods:{
        // 获取数据
        getMedia() {
            fetchGroup(this.listQuery).then(res => {
                console.log(res)
                if(res.code == 20000) {
                    this.list = res.data.items
                    this.total = res.data.total
                }
            })
        },

        // 上架下架
        changeStatus (index, row) {
            // updateAudio().then(res => {
            //     if(res.code == 20000) {
            //         this.list[index].status = row.status ? 0 : 1;
            //         this.$message.success('操作'+res.data)
            //     } else {
            //         this.$message.error('操作'+res.data)
            //     }
            // })
            row.time_status = '已下架'

        },

        search () {
            fetchList(this.listQuery).then(res => {
                if(res.code == 20000) {
                    this.$message.success("搜索成功")
                    this.list = res.data.items
                    this.total = res.data.total
                }
            })
        },

        delList (index, row) {
            console.log("!!!!!")
            this.currentindex = index
            this.visible = true
        },

        delListJudge () {
            this.list.splice(this.currentindex, 1)
            this.$notify({
                title: '提示',
                message: '删除成功',
                duration: 0
            });
        },

        onSubmit(formName) {
           
            this.$refs[formName].validate((valid) => {
            if (valid) {
                if(!this.isEdit) {
                    let time = new Date().getTime()
                    this.form.created_time = time
                    let i = Math.floor(Math.random() * 100 + Math.random() * 1000 + Math.random() * 10000);
                    this.form.id = i
                    this.list.unshift(this.form)

                } else {
                    let index = this.list.findIndex(item => {
                        return item.id == this.form.id
                    })
                    this.list[index] = this.form
                }
                
                this.$message.success(this.isEdit ? '编辑': '添加'+ "成功")
                this.dialogFormVisible = false
                this.form = Object.assign({}, formDefault)

            } else {
                this.$message.error(this.isEdit ? '编辑': '添加'+ "失败")
                return false;
            }
            });
        },

        editData (index, row) {
            console.log(row)
            this.form = this.list[index]
            this.dialogFormVisible = true
            console.log("edit", this.form)
            this.isEdit = true
        },


        addMedia () {
            this.form = Object.assign({}, formDefault)
            console.log("add", this.form)
            this.dialogFormVisible = true
            this.isEdit = false

        },

        // 每页条数改变的时候触发
        handleSizeChange (val) {
            this.listQuery.page = 1
            this.listQuery.limit = val
            this.getMedia()
        },
        // page改变的时候触发
        handleCurrentChange (val) {
            this.listQuery.page = val
            this.getMedia()
        }
    },

    filters: {
        
        filterStatus (value) {
            if(value < 2) {
                return '活动中'
            } else if(value >=2 && value < 5) {
                return '未开始'
            } else if(i > 5) {
                return '已结束'
            }
        }
    }
}
</script>

<style scoped>
.wrapper {
    padding: 20px;
}
.search {
    display: flex;
    justify-content: space-between;
    padding-bottom: 20px;

}
.search .right {
    display: flex;

}
.media-left {
    display: inline-block;
}
.media-right {
    display: inline-block;
    margin-left: 15px;
    line-height: 1.2em;
}
.block {
    margin-top: 20px;
}
.auto {
    scroll-behavior: auto;
}
</style>