<template>
    <div class="wrapper">
        <el-button type="primary" @click="addList">新增收款账号</el-button>
        <div class="table" >
            <el-table border :data="list">
                <el-table-column label="账号 " align="center">
                    <template slot-scope="scope">
                        {{ scope.row.account }}
                    </template>
                </el-table-column>
                <el-table-column label="开户人" align="center">
                    <template slot-scope="scope">
                        {{ scope.row.name }}
                    </template>
                </el-table-column>
                <el-table-column label="开户行" align="center"> 
                    <template slot-scope="scope">
                        {{ scope.row.path }}
                    </template>
                </el-table-column>
                <el-table-column label="操作" align="center">
                    <template slot-scope="scope">
                        <el-button type="primary" @click="editList(scope.$index, scope.row)">编辑</el-button>
                        <el-button type="danger" >删除</el-button>
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




        <el-dialog title="新增" :visible.sync="dialogFormVisible">
            <el-form :model="form"  :rules="rules">
                <el-form-item label="账号" :label-width="formLabelWidth" prop="account" style="width: 50%" >
                    <el-input v-model="form.account"></el-input>
                </el-form-item>

                <el-form-item label="活动区域" :label-width="formLabelWidth">
                    <el-select v-model="form.region" placeholder="请选择活动区域">
                        <el-option label="区域一" value="shanghai"></el-option>
                        <el-option label="区域二" value="beijing"></el-option>
                    </el-select>
                </el-form-item>

                <el-form-item label="详细地址" :label-width="formLabelWidth" prop="address" style="width: 50%">
                    <el-input v-model="form.address" ></el-input>
                </el-form-item>

                <el-form-item label="所属银行" :label-width="formLabelWidth" prop="path" style="width: 50%" >
                    <el-select v-model="value" >
                        <el-option
                            v-for="item in options"
                            :key="item.value"
                            :label="item.label"
                            :value="item.value">
                        </el-option>
                    </el-select>
                </el-form-item>

                <el-form-item label="姓名" :label-width="formLabelWidth" prop="name" style="width: 50%">
                    <el-input v-model="form.account" autocomplete="off"></el-input>
                </el-form-item>
               
            </el-form>

            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false">取 消</el-button>
                <el-button type="primary" @click="dialogFormVisible = false">确 定</el-button>
            </div>
        </el-dialog>
    </div>
</template>

<script>
import { fetchPayments } from '../../api/pay'


const defaultQuery = {
    page: 1,
    limit:20,
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
            form:{},
            formLabelWidth:'100px',
            rules:{
                account:[
                    {required: true, message:"请输入账号", trigger: "blur"}
                ],
                address:[
                    {required: true, message:"请输入地址", trigger: "blur"}
                ],
                path:[
                    {required: true, message:"请选择银行", trigger: "blur"}
                ],
                name:[
                    {required: true, message:"请输入姓名", trigger: "blur"}
                ]
            },
            options: [{
                value: '选项1',
                label: '黄金糕'
                }, {
                value: '选项2',
                label: '双皮奶'
                }, {
                value: '选项3',
                label: '蚵仔煎'
                }, {
                value: '选项4',
                label: '龙须面'
                }, {
                value: '选项5',
                label: '北京烤鸭'
                }],
            value: ''
            }
    },

    mounted () {
        this.getPayment()
    },

    methods:{
        getPayment () {
              fetchPayments(this.listQuery).then(res => {
                console.log(res)
                if(res.code == 20000) {
                    this.list = res.data.items
                    this.total = res.data.total
                }
            })
        },

        editList (index, row) {
            console.log(index, row)
            this.dialogFormVisible == true
        },

        addList () {
            this.dialogFormVisible = true
        },




            // 每页条数改变的时候触发
        handleSizeChange (val) {
            this.listQuery.page = 1
            this.listQuery.limit = val
            this.getPayment()
        },
        // page改变的时候触发
        handleCurrentChange (val) {
            this.listQuery.page = val
            this.getPayment()
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