<template>
    <div class="wrapper">

        <div class="search">
            <div>
                <el-button type="primary" @click="exportData"><i class="el-icon-edit" />导出选中</el-button>
            </div>
            <div class="right">
                <el-input placeholder="用户名" v-model="listQuery.title"></el-input>
                <el-button type="primary" @click="search"><i class="el-icon-search" />搜索</el-button>
            </div>
        </div>

        <el-table :border="true" :data="list" @selection-change="handleSelectionChange" ref="multipleTable"> 
            <!-- <el-table-column align="center" width="50" type="selection">    -->
            <el-table-column type="selection" align="center"></el-table-column>
            <el-table-column label="订单号" align="center" width="100">      
                <template slot-scope="scope">
                    {{ scope.row.id }}
                </template>
            </el-table-column>
            <el-table-column label="商品名称" class="media" align="center">
                <template slot-scope="scope" >
                   {{ scope.row.goods[0].title }}
                </template>
            </el-table-column>
            <el-table-column label="订单类型" align="center" width="100">  
                <template slot-scope="scope">
                    {{ scope.row.type | filterTypeOrder}}
                </template>
            </el-table-column>
            <el-table-column label="订单状态" align="center" width="150">  
                <template slot-scope="scope">
                    <el-tag :type="(scope.row.status === 'success') ? 'success' : 'danger'"> {{ scope.row.status | filterStatus }}</el-tag>
                </template>
            </el-table-column>
            <el-table-column label="原价/实付（元）" align="center" width="150">  
                <template slot-scope="scope">
                    <span style="color: #ccc">{{ scope.row.total_price }}</span><span style="color: red">/{{ scope.row.price }}</span> 
                </template>
            </el-table-column>
            <el-table-column label="支付方式" align="center" width="150">  
                <template slot-scope="scope">
                    {{ scope.row.pay_method === 'wechat' ? "微信支付" : "其他" }}
                </template>
            </el-table-column>
            <el-table-column label="创建/支付时间" align="center" width="200">
                <template slot-scope="scope">
                    {{ scope.row.created_time }}<br>
                    {{ scope.row.updated_time }}
                </template>
            </el-table-column>
            <el-table-column label="操作" align="center" width="200">
                <template slot-scope="scope">
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
                        <el-button slot="reference" @click="delList(scope.$index, scope.row)" type="danger">删除</el-button>
                    </el-popover>
                </template>
            </el-table-column>
        </el-table>

        
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

import { fetchOrder } from '../../api/pay'
import Singlemage from '../../components/Upload/SingleImage'
import Tinymce from '@/components/Tinymce'

const listQuery = {
    page:1,
    limit: 20,
    title: '',
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
            // 多选
            options:[],
            value:null,
            total:null,
            list: [],
            listQuery: Object.assign({}, listQuery),
            dialogFormVisible: false,
            formLabelWidth: '80',
            form: Object.assign({}, formDefault),
            isEdit: false,
            visible: false,
            currentindex:null,

        }
    },

    mounted () {
        this.getOrder()
    },

    methods:{
        // 获取数据
        getOrder() {
            fetchOrder(this.listQuery).then(res => {
                console.log(res)
                if(res.code == 20000) {
                    this.list = res.data.items
                    this.total = res.data.total
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

        delListJudge (index) {
            // this.$refs.closepopover.click();
            this.$refs[`popover-${index}`].doClose()
            this.list.splice(this.currentindex, 1)
            this.$notify({
                title: '提示',
                message: '删除成功',
                duration: 0,
                type:'success'
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

     


        exportData () {
        
            if (this.options.length) {
                    // this.downloadLoading = true
                    import('@/vendor/Export2Excel').then(excel => {
                    const tHeader = ['订单号', '商品名称', '订单类型', '订单状态', '原价元()', '实付(元)', '支付方式', '创建时间', '支付时间']
                    const filterVal = ['id', 'title', 'type', 'status', 'total_price', 'price', 'pay_method', 'created_time', 'updated_time']
                    const list = this.options
                    list.forEach(item => {
                        if(item.type == 'group') {
                            item.type = "拼团"
                        } else {
                            item.type = "普通"
                        }

                        if(item.status == 'success') {
                            item.status =  '成功'
                        } else if(item.status == 'fail') {
                            item.status =  '失败'
                        } else if(item.status == 'pendding') {
                            item.status =  '待支付'
                        }

                        item.pay_method = "微信支付"

                    })
                    const data = this.formatJson(filterVal, list)
                    excel.export_json_to_excel({
                        header: tHeader,
                        data,
                        filename: this.filename
                    })
                    this.$refs.multipleTable.clearSelection()
                    // this.downloadLoading = false
                    })
                } else {
                this.$message({
                    message: '请至少选中一项',
                    type: 'warning'
                })
            }

        },

        formatJson(filterVal, jsonData) {
            return jsonData.map(v => filterVal.map(j => v[j]))
        },

        handleSelectionChange (val) {
            this.options = val
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
            this.getOrder()
        }
    },

    filters: {
        
        filterStatus (value) {
            if(value == 'success') {
                return '成功'
            } else if(value == 'fail') {
                return '失败'
            } else if(value == 'pendding') {
                return '待支付'
            }
        },

        filterTypeOrder (value) {
              if(value == 'group') {
                return '拼团'
            } else {
                return '普通'
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