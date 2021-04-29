<template>
    <div class="wrapper">
        <el-button type="primary" @click="addList"><i class="el-icon-edit" />新增子页面</el-button>
        <div class="table" >
            <el-table border :data="list">
                <el-table-column label="页面内容" align="center">
                    <template slot-scope="scope">
                        {{ scope.row.title }}
                    </template>
                </el-table-column>
                <el-table-column label="创建时间" align="center">
                    <template slot-scope="scope">
                        {{ scope.row.created_time }}
                    </template>
                </el-table-column>
                <el-table-column label="更新时间" align="center"> 
                    <template slot-scope="scope">
                        {{ scope.row.updated_time }}
                    </template>
                </el-table-column>
                <el-table-column label="操作" align="center">
                    <template slot-scope="scope">
                        <el-button type="warning" @click="mobileEdit(scope.$index, scope.row)">装修</el-button>
                        <el-button type="primary" @click="editList(scope.$index, scope.row)">编辑</el-button>
                        <el-popover
                            placement="top"
                            :ref="`popover-${scope.$index}`"
                            width="160"
                            trigger="click"
                        >
                        <p><i class="el-icon-question" style="color: red"></i>确定要删除这行数据吗？</p>
                        <div style="text-align: right; margin: 0">
                            <el-button size="mini" type="text"    @click="scope._self.$refs[`popover-${scope.$index}`].doClose()">取消</el-button>
                            <el-button type="primary" size="mini" @click="delListJudge(scope.$index)" >确定</el-button>
                        </div>
                        <el-button type="danger" slot="reference"  :disabled="scope.row.type == 'index'" style="margin-left: 10px">删除</el-button>
                    </el-popover>
                    </template>
                </el-table-column>
            </el-table>
        </div>

         <div class="block">
            <el-pagination
                background
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="listQuery.page"
                :page-sizes="[20, 30, 40, 50]"
                :page-size="listQuery.limit"
                layout="total, sizes, prev, pager, next, jumper"
                :total="total"
            >
            </el-pagination>
        </div>




        <el-dialog :title=" isEdit ? '编辑':'新增'" :visible.sync="dialogFormVisible">
            <el-form :model="form"  :rules="rules">
                <el-form-item label="页面标题" :label-width="formLabelWidth" prop="title" style="width: 50%" >
                    <el-input v-model="form.title"></el-input>
                </el-form-item>
            </el-form>

            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false">取 消</el-button>
                <el-button type="primary" @click="addOrEditForm">确 定</el-button>
            </div>
        </el-dialog>
    </div>
</template>

<script>

import { fetchMobileList } from  "../../api/renovation"

const defaultQuery = {
    page: 1,
    limit:20,
}

const defaultForm = {
    title: "",
    created_time: null,
    updated_time: null,
    type:"",
    isdefault: 1,
    id: null
}


export default {
    components:{
    },
    data () {
        return {
            listQuery: Object.assign({}, defaultQuery),
            list:[],
            total:10,
            dialogFormVisible: false,
            form:Object.assign({}, defaultForm),
            formLabelWidth:'100px',
            rules:{
                title:[
                    {required: true, message:"请输入账号", trigger: "blur"}
                ],
            },
            isEdit: false,
            currentIndex: null,
        }
    },

    mounted () {
        this.geMobile()
    },

    methods:{
        geMobile () {
              fetchMobileList(this.listQuery).then(res => {
                console.log(res)
                if(res.code == 20000) {
                    this.list = res.data.items
                    this.total = res.data.total
                }
            })
        },

        editList (index, row) {
            this.dialogFormVisible = true
            this.form.title = this.list[index].title
            this.isEdit = true
            this.currentIndex = index
        },

        addList () {
            this.dialogFormVisible = true
            this.form = Object.assign({}, defaultForm)
            this.isEdit = false
        },


        mobileEdit (index, row) {
            this.$router.push({path: "/renovation/mobile_edit", query:{id: row.id}})
        },

        // 删除行内数据
        delListJudge (index) {
            console.log(index)
        },

        // 添加或者编辑行内数据
        addOrEditForm () {
            let time = new Date().toLocaleString().split('/').join('-'); 

            if(this.isEdit) {
                // 编辑
                this.list[this.currentIndex].title = this.form.title
                this.list[this.currentIndex].updated_time = time
                this.dialogFormVisible = false
                return
            }
            let data = {
                created_time: time,
                updated_time: time,
                title: this.form.title,
                type:"",
                isdefault: 1,
                id: this.list[this.list.length-1].id + 1
            }
            this.list.push(data)

            this.dialogFormVisible =  false
        },




            // 每页条数改变的时候触发
        handleSizeChange (val) {
            this.listQuery.page = 1
            this.listQuery.limit = val
            this.geMobile()
        },
        // page改变的时候触发
        handleCurrentChange (val) {
            this.listQuery.page = val
            this.geMobile()
        },
    },

    filters: {
    }
}
</script>

<style scoped>
.wrapper {
    padding: 20px
}
.block {
    margin-top: 20px;
}
.table {
    margin-top: 20px;
}
</style>