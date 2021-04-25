<template>
    <div class="wrapper">
        <div class="card">
            <el-row :gutter="20">
                <el-col :span="6">
                    <div class="grid-content bg-purple">
                        <el-card shadow="never">
                            <div slot="header" class="clearfix">
                                <span>可用余额（元）</span>
                                <el-button style="float: right; margin-top: -10px" type="primary" size="mini" @click="apply">申请提现</el-button>
                            </div>
                            <div class="font">
                                20.00
                            </div>
                        </el-card>
                    </div>
                </el-col>
                <el-col :span="6">
                    <div class="grid-content bg-purple">
                        <el-card shadow="never">
                            <div slot="header" class="clearfix">
                                <span>累计收入（元）</span>
                            </div>
                            <div class="font">
                                20.00
                            </div>
                        </el-card>
                    </div>
                </el-col>
                <el-col :span="6">
                    <div class="grid-content bg-purple">
                        <el-card shadow="never">
                            <div slot="header" class="clearfix">
                                <span>待结算金额（元）</span>
                            </div>

                            <div class="font">
                                20.00
                            </div>
                        </el-card>
                    </div>
                </el-col>
                <el-col :span="6">
                    <div class="grid-content bg-purple">
                        <el-card shadow="never">
                            <div slot="header" class="clearfix">
                                <span>冻结金额（元）</span>
                            </div>

                            <div class="font">
                                20.00
                            </div>
                        </el-card>
                    </div>
                </el-col>
            </el-row>
        </div> 

        <div class="table">
            <template>
                <el-table
                    :data="list"
                    style="width: 100%"
                    border
                >
                    <el-table-column
                        label="交易时间"
                        align="center"
                    >
                        <template slot-scope="scope">
                            {{ scope.row.created_time }}
                        </template>

                    </el-table-column>
                    <el-table-column
                        label="提款账号"
                        align="center"
                    >
                        <template slot-scope="scope">
                            {{ scope.row.account }}
                        </template>
                    </el-table-column>
                    <el-table-column
                        label="开户人"
                        align="center"
                    >
                        <template slot-scope="scope">
                            {{ scope.row.name }}
                        </template>
                    </el-table-column>
                    <el-table-column
                        label="提款现金"
                        align="center"
                    >
                        <template slot-scope="scope">
                            ￥{{ scope.row.price }}
                        </template>
                    </el-table-column>
                    <el-table-column
                        label="状态"
                        align="center"
                    >
                        <template slot-scope="scope">
                            <el-tag :type="scope.row.status ? 'success':'warning'">{{ scope.row.status | filterStatus }}</el-tag> 
                        </template>
                    </el-table-column>
                </el-table>
            </template>
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

        <!-- 申请体现弹框 -->
        <el-dialog title="体现" :visible.sync="dialogFormVisible" >
            <el-form :model="form" :rules="rules">
                <el-form-item label="提现金额" :label-width="formLabelWidth">
                    <el-input-number v-model="form.num" @change="handleChange" :min="1"></el-input-number>
                </el-form-item>
                <el-form-item label="提现账户" :label-width="formLabelWidth" prop="account">
                    <el-select v-model="form.account" placeholder="请选择活动区域">
                        <el-option label="区域一" value="shanghai"></el-option>
                        <el-option label="区域二" value="beijing"></el-option>
                    </el-select>
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

import { fetchAssets } from '../../api/pay'

const defaultQuery = {
    page: 1,
    limit:20,
}

export default {
    components:{
    },
    data () {
        return {
            list:[],
            total:20,
            listQuery: Object.assign({}, defaultQuery),
            dialogFormVisible: false,
            // 体现弹框数据
            form:{},
            formLabelWidth:'80px',
            form:{
                num: 1,
            },
            rules:{
                account:[
                    {required: true, message: "请选择内容", trigger: 'blur'}
                ]
            }
        }
    },

    mounted () {
        this.getAssest()
    },

    methods:{
        getAssest () {
            fetchAssets(this.listQuery).then(res => {
                console.log(res)
                if(res.code == 20000) {
                    this.list = res.data.items
                    this.total = res.data.total
                }

                console.log(this.list)
            })
        },
         // 每页条数改变的时候触发
        handleSizeChange (val) {
            this.listQuery.page = 1
            this.listQuery.limit = val
            this.getAssest()
        },
        // page改变的时候触发
        handleCurrentChange (val) {
            this.listQuery.page = val
            this.getAssest()
        },

        apply () {
            this.dialogFormVisible = true
        },

        handleChange(value) {
            console.log(value);
        },
    },

    filters: {

        filterStatus (val) {
            if(val == 0) {
                return '审核中'
            } else if(val == 1) {
                return '提现成功'
            }
        }
    }
}
</script>

<style scoped>
.wrapper {
    padding: 20px;
}
.card {
    width: 100%;
}
.font {
    font-size: 30px;
    font-weight: bolder;
}
.table {
    margin-top: 20px;
}
.block {
    margin-top: 20px;
}
</style>