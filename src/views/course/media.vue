<template>
    <div class="wrapper">

        <div class="search">
            <div>
                <el-button type="primary" @click="addMedia"><i class="el-icon-edit" />新增图文</el-button>
            </div>
            <div class="right">
                  <el-select v-model="listQuery.status" placeholder="商品状态" clearable>
                    <el-option
                        v-for="item in options"
                        :key="item.value"
                        :label="item.label"
                        :value="item.value"
                    >
                    </el-option>
                </el-select>
                <el-input placeholder="标题" v-model="listQuery.title"></el-input>
                <el-button type="primary" @click="search"><i class="el-icon-search" />搜索</el-button>
            </div>
        </div>

        <el-table :border="true" :data="list"> 
            <el-table-column label="ID" align="center" width="100">      
                <template slot-scope="scope">
                    {{ scope.row.id }}
                </template>
            </el-table-column>
            <el-table-column label="图文内容" class="media">
                <template slot-scope="scope" >
                    <div class="media-left">
                        <img :src="scope.row.cover" alt="" style="width: 100px">
                    </div>
                    <div class="media-right">
                        <p>{{ scope.row.title }}</p>
                        <p style="color:red">￥{{ scope.row.price }}</p>
                    </div>
                </template>
            </el-table-column>
            <el-table-column label="订阅量" align="center" width="150">  
                <template slot-scope="scope">
                    {{ scope.row.sub_count }}
                </template>
            </el-table-column>
            <el-table-column label="状态" align="center" width="150">  
                <template slot-scope="scope">
                    <el-tag :type="scope.row.status ? 'success' : 'danger'"> {{ scope.row.status | filterStatus }}</el-tag>
                </template>
            </el-table-column>
            <el-table-column label="创建时间" align="center" width="200">
                <template slot-scope="scope">
                    {{ scope.row.created_time }}
                </template>
            </el-table-column>
            <el-table-column label="操作" align="center" width="300">
                <template slot-scope="scope">
                    <el-button size="mini" type="primary" @click="editData(scope.$index, scope.row)">编辑</el-button>
                    <el-button size="mini" @click="chanStatus(scope.$index, scope.row)" :type="!scope.row.status ? 'success' : ''">{{ scope.row.status ? '下架' : '上架'}}</el-button>
                    <el-popover
                        placement="top"
                        width="160"
                        trigger="hover"
                    >
                        <p>这是一段内容这是一段内容确定删除吗？</p>
                        <div style="text-align: right; margin: 0">
                            <el-button type="danger" size="mini" @click="delListJudge">确定</el-button>
                        </div>
                        <el-button size="mini" type="danger" @click="delList(scope.$index, scope.row)" slot="reference" style="margin-left: 10px">删除</el-button>
                    </el-popover>
                    
                </template>
            </el-table-column>
        </el-table>

        <!-- 编辑与添加 -->
       <el-dialog :title="isEdit ?  '编辑':'添加'" :visible.sync="dialogFormVisible" fullscreen>
            <el-form ref="form" :model="form" label-width="150px" :rules="rules">
                <el-form-item label="标题" prop="title">
                    <el-input v-model="form.title" style="width: 30%"></el-input>
                </el-form-item>
                <el-form-item label="封面" style="width: 50%">
                    <Singlemage></Singlemage>
                </el-form-item>
                <el-form-item label="试看内容" style="width: 50%" prop="try">
                    <Tinymce v-model="form.try"></Tinymce>
                </el-form-item>
                <el-form-item label="课程介绍"  style="width: 50%" prop="content">
                    <Tinymce v-model="form.content"></Tinymce>
                </el-form-item>
                <el-form-item label="课程价格" >
                   <el-input-number v-model="form.price" :step="1" :min='0'></el-input-number>
                </el-form-item>
                <el-form-item label="状态">
                    <el-radio v-model="form.status" :label="0">下架</el-radio>
                    <el-radio v-model="form.status" :label="1">上架</el-radio>
                </el-form-item>
                <el-form-item>
                    <el-button type="primary" @click="onSubmit('form')">保存</el-button>
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

        <!-- 删除 -->
        <el-popover
            placement="top"
            width="160"
            trigger="click"
        >
            <p>这是一段内容这是一段内容确定删除吗？</p>
            <div style="text-align: right; margin: 0">
                <el-button size="mini" type="text" @click="visible = false">取消</el-button>
                <el-button type="primary" size="mini" @click="delListJudge">确定</el-button>
            </div>
            <el-button slot="reference">删除</el-button>
        </el-popover>
    </div>
</template>

<script>

import { fetchList, updateMedia } from '../../api/media'
import Singlemage from '../../components/Upload/SingleImage'
import Tinymce from '@/components/Tinymce'

const listQuery = {
    page:1,
    limit: 20,
    title: '',
    sort: false,
    status: null,
}

const formDefault = {
    id:null,
    title: '',
    num: 5,
    price: null,
    created_time: null,
    try:null,
    content:null,
    cover:"http://dummyimage.com/200x100",
    status: 1,
}

export default {
    components:{
        Singlemage,
        Tinymce,
    },
    data () {
        return {
            options:[
                {value: 0, label: '已下架'},
                {value: 1, label: '已上架'},
            ],

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
            fetchList(this.listQuery).then(res => {
                console.log(res)
                if(res.code == 20000) {
                    this.list = res.data.items
                    this.total = res.data.total
                }
            })
        },

        // 上架下架
        chanStatus (index, row) {
            updateMedia(this.listQuery).then(res => {
                if(res.code == 20000) {
                    this.list[index].status = row.status ? 0 : 1;
                    this.$message.success('操作'+res.data)
                } else {
                    this.$message.error('操作'+res.data)
                }
            })

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
            if(value == 1) {
                return '已上架'
            } else {
                return '已下架'
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