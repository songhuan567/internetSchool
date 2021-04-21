<template>
    <div class="wrapper">

        <el-card style="margin-bottom: 20px" shadow="never">
            <div class="column">
                <div style="width: 200px; height: 100px; background-color: skyblue; margin: 20px">
                    <img :src="viewdata.cover" alt="" >
                </div>
                <div class="colu__bot">
                    <div class="top">
                        <h3>{{ viewdata.title }}</h3>
                        <p style="color:#bbb">{{ viewdata.isend ? '已完结':'连载中' }}</p>
                    </div>
                    <span style="color: #bbb; font-size: 14px">{{ viewdata.content }}</span>
                    <p style="color: red">￥{{ viewdata.price }}</p>
                    <el-button type="warning" size="mini" @click="changeStatus(viewdata.status)">{{ viewdata.status ? "上架":"下架"}}</el-button>
                    <el-button style="margin-left: -9px" size="mini" @click="changeIsend(viewdata.isend)">{{ !viewdata.isend ? '设为已完结':'设为连载中' }}</el-button>
                </div>
            </div>
        </el-card>

        <div class="search">
            <div>
                <el-button type="primary" @click="addMedia"><i class="el-icon-edit" />新增目录</el-button>
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
            <el-table-column label="专栏内容" class="media">
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
          
            <el-table-column label="操作" align="center" width="300">
                <template slot-scope="scope">
                    <el-button size="mini" type="primary" @click="editData(scope.$index, scope.row)">编辑</el-button>
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

        <!-- 新增目录 -->
       <el-dialog :title="isEdit ?  '编辑':'添加'" :visible.sync="dialogFormVisible" style="scroll-behavior: auto; ">
            <!-- 搜索 -->
            <div style="float: right">
                <el-input placeholder="标题" style="width: 200px" clearable v-model="tableQuery.title"></el-input>
                <el-button type="primary" style="margin-left: 10px" @click="search">搜索</el-button>
            </div>
    
            <el-tabs tab-position="left" style="height: 400px;margin-top:50px; " @tab-click = "changeTab" >
                <el-tab-pane label="图文">
                    <el-table
                        border
                        :data="tableData"
                        tooltip-effect="dark"
                        style="width: 100%"
                        max-height="400"
                        @selection-change="handleSelectionChange"
                    >
                        <el-table-column
                            type="selection"
                            width="55"
                        >
                        </el-table-column>
                        <el-table-column
                            label="内容"
                        >
                            <template slot-scope="scope"> 
                                <div class="media-left">
                                    <img :src="scope.row.cover" alt="" style="width: 100px">
                                </div>
                                <div class="media-right">
                                    <p>{{ scope.row.title }}</p>
                                    <p style="color:red">￥{{ scope.row.price }}</p>
                                </div>
                            </template>
                        </el-table-column>
                 
                    </el-table>


                </el-tab-pane>
                <el-tab-pane label="音频">
                     <el-table
                        border
                        :data="tableData"
                        tooltip-effect="dark"
                        style="width: 100%"
                        @selection-change="handleSelectionChange"
                    >
                        <el-table-column
                            type="selection"
                            width="55"
                        >
                        </el-table-column>
                        <el-table-column
                            label="内容"
                        >
                            <template slot-scope="scope"> 
                                <div class="media-left">
                                    <img :src="scope.row.cover" alt="" style="width: 100px">
                                </div>
                                <div class="media-right">
                                    <p>{{ scope.row.title }}</p>
                                    <p style="color:red">￥{{ scope.row.price }}</p>
                                </div>
                            </template>
                        </el-table-column>
                 
                    </el-table>
                    
                </el-tab-pane>
                <el-tab-pane label="视频">
                     <el-table
                        border
                        :data="tableData"
                        tooltip-effect="dark"
                        style="width: 100%"
                        @selection-change="handleSelectionChange"
                    >
                        <el-table-column
                            type="selection"
                            width="55"
                        >
                        </el-table-column>
                        <el-table-column
                            label="内容"
                        >
                            <template slot-scope="scope"> 
                                <div class="media-left">
                                    <img :src="scope.row.cover" alt="" style="width: 100px">
                                </div>
                                <div class="media-right">
                                    <p>{{ scope.row.title }}</p>
                                    <p style="color:red">￥{{ scope.row.price }}</p>
                                </div>
                            </template>
                        </el-table-column>
                 
                    </el-table>

                </el-tab-pane>
            </el-tabs>
            <!-- 页码 -->
            <div class="block">
                <el-pagination
                    background
                    @size-change="handleSizeChange"
                    @current-change="handleCurrentChange"
                    :current-page="tableData.page"
                    :page-sizes="[20, 30, 40, 50]"
                    :page-size="tableData.limit"
                    layout="total, sizes, prev, pager, next, jumper"
                    :total="total"
                >
                </el-pagination>
            </div>
            <!-- 提交 -->
            <div style="float: right; margin-top: -15px; height:80px;">
                <el-button @click="dialogFormVisible =  false">取消</el-button>
                <el-button type="primary" @click="submit">确定</el-button>
            </div>
        </el-dialog>

    </div>
</template>

<script>

import { fetchDetailCourse, fetchDetail } from '../../api/column'
import { fetchList as mediaList } from '../../api/media'
import { fetchList as videoList } from '../../api/video'
import { fetchList as audioList } from '../../api/audio'
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
    content:null,
    cover:"http://dummyimage.com/200x100",
    status: 1,
}


const viewData = {
    content: "",
    cover: "",
    created_time: "",
    id: null,
    isend:null, 
    price: null,
    status: null,
    sub_count: null,
    title: "",
    try: "",
    updated_time: "",
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
            tableQuery: Object.assign({}, listQuery),
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
            viewdata: Object.assign({}, viewData),
            tableData:[],
            // 当前tab项
            currentNum:'0',
            // 当前 options
            currentOptions:[],
        }
    },

    mounted () {
        this.getMedia()
        this.column_detail()
    },

    methods:{

        column_detail () {
            fetchDetail({id: this.$route.query.id}).then(res => {
                console.log(res)
                this.viewdata = res.data
            })
        },
        // 获取数据
        getMedia() {
            fetchDetailCourse(this.listQuery).then(res => {
                console.log(res)
                if(res.code == 20000) {
                    this.list = res.data.items
                    this.total = res.data.total
                }
            })
        },

        // 上架下架
        changeStatus (status) {
           this.viewdata.status = status ? 0 : 1
        },

        // 是否完结
        changeIsend (isend) {
            this.viewdata.isend = isend ? 0 : 1
        },

        // 页面搜索
        search () {
            fetchList(this.listQuery).then(res => {
                if(res.code == 20000) {
                    this.$message.success("搜索成功")
                    this.list = res.data.items
                    this.total = res.data.total
                }
            })
        },

        // 删除操作
        delList (index, row) {
            console.log("!!!!!")
            this.currentindex = index
            this.visible = true
        },

        // 删除提示
        delListJudge () {
            this.list.splice(this.currentindex, 1)
            this.$notify({
                title: '提示',
                message: '删除成功',
                duration: 0
            });
        },

        // tab选择项卡 点击的时候触发的操作
        changeTab (tab) {
            this.tableQuery.page = 1
            this.tableQuery.limit = 20
            let num = tab.paneName
            this.currentNum = num
            if(num == '0') {
                mediaList(this.tableQuery).then(res => {
                    this.tableData = res.data.items
                })
            } else if(num == '1') {
                videoList(this.tableQuery).then(res => {
                    this.tableData = res.data.items
                })
            } else if(num == '2') {
                audioList(this.tableQuery).then(res => {
                    this.tableData = res.data.items
                })
            }
        },

        // 点击前面的多选触发的操作
        handleSelectionChange (val) {
            console.log(val)
            this.currentOptions = val
        },

        // 新增目录的提交操作
        submit () {
            if(this.currentOptions.length < 1) {
                this.$message.error('至少选中一个')
            } else {
                this.list.push(...this.currentOptions)
                this.dialogFormVisible = false
            }
        },
     

        // 新增目录
        addMedia () {
            this.form = Object.assign({}, formDefault)
            console.log("add", this.form)
            this.dialogFormVisible = true
            this.isEdit = false
            mediaList(this.tableQuery).then(res => {
                    this.tableData = res.data.items
            })

        },

        // 每页条数改变的时候触发
        handleSizeChange (val) {
            this.tableData.page = 1
            this.tableData.limit = val
            if(this.currentNum == '0') {
                mediaList(this.tableQuery).then(res => {
                    this.tableData = res.data.items
                })
            } else if(this.currentNum  == '1') {
                videoList(this.tableQuery).then(res => {
                    this.tableData = res.data.items
                })
            } else if(this.currentNum  == '2') {
                audioList(this.tableQuery).then(res => {
                    this.tableData = res.data.items
                })
            }
        },
        // page改变的时候触发
        handleCurrentChange (val) {
            this.tableData.page = val
             if(this.currentNum == '0') {
                mediaList(this.tableQuery).then(res => {
                    this.tableData = res.data.items
                })
            } else if(this.currentNum  == '1') {
                videoList(this.tableQuery).then(res => {
                    this.tableData = res.data.items
                })
            } else if(this.currentNum  == '2') {
                audioList(this.tableQuery).then(res => {
                    this.tableData = res.data.items
                })
            }
        },

        // 目录里面的搜索
        search () {
            if(this.currentNum == '0') {
                mediaList(this.tableQuery).then(res => {
                    this.tableData = res.data.items
                })
            } else if(this.currentNum  == '1') {
                videoList(this.tableQuery).then(res => {
                    this.tableData = res.data.items
                })
            } else if(this.currentNum  == '2') {
                audioList(this.tableQuery).then(res => {
                    this.tableData = res.data.items
                })
            }
        }


       
    },

    computed:{
        // isshow () {
        //     return !!this.$route.query.id
        // }
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
.column{
    display: flex;
}
.top {
    width: 100%;
    display: flex;
    justify-content: space-between;
}
.colu__bot {
    flex: 1;
}

.media-left {
    display: inline-block;
}
.media-right {
    display: inline-block;
    margin-left: 15px;
    line-height: 1.2em;
}
</style>