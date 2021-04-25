<template>
    <div class="wrapper">

        <div class="search">
            <div>
                <el-dropdown  @command="handleCommand">
                    <el-button type="danger" >黑名单设置<i class="el-icon-arrow-down" /></el-button>
                    <el-dropdown-menu slot="dropdown">
                        <el-dropdown-item command="no_comment">禁止评论</el-dropdown-item>
                        <el-dropdown-item command="no_access">禁止访问</el-dropdown-item>
                    </el-dropdown-menu>
                </el-dropdown>
            </div>
            <div class="right">
                <el-input placeholder="标题" v-model="listQuery.title"></el-input>
                <el-button type="primary" @click="search"><i class="el-icon-search" />搜索</el-button>
            </div>
        </div>

        <el-table :border="true" :data="list" @selection-change="handleSelectionChange"> 
            <el-table-column align="center" width="50" type="selection" >      
            </el-table-column>
            <el-table-column label="用户" class="media">
                <template slot-scope="scope" >
                    <div class="media-left">
                        <el-avatar :size="50" :src="scope.row.user.avatar" style="margin-right:10px"></el-avatar>
                        <span>{{ scope.row.user.username }}</span>
                    </div>
                </template>
            </el-table-column>
            <el-table-column label="消费总额" align="center" width="150">  
                <template slot-scope="scope">
                    {{ scope.row.total_consume }}
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
                    <el-button size="mini" type="success" style="margin-right:10px">联系用户</el-button>
                    <el-dropdown @command="handleCommand" trigger="click">
                        <el-button type="danger" size="mini" @click="getcurrentIndex(scope.$index)">黑名单设置<i class="el-icon-arrow-down" /></el-button>
                        <el-dropdown-menu slot="dropdown" >
                            <el-dropdown-item  command="no_comment" >禁止评论</el-dropdown-item>
                            <el-dropdown-item  command="no_access"  >禁止访问</el-dropdown-item>
                        </el-dropdown-menu>
                    </el-dropdown>

                </template>
            </el-table-column>
        </el-table>

        <!-- 编辑 -->
       <el-dialog title="用户详情" :visible.sync="dialogFormVisible" width="70%">
           <div style="margin-bottom: 20px">
                <el-row :gutter="20">
                    <el-col :span="6"><div class="grid-content bg-purple">ID:{{formDetail.id}}</div></el-col>
                    <el-col :span="6"><div class="grid-content bg-purple">用户名:{{formDetail.user.username}}</div></el-col>
                    <el-col :span="6"><div class="grid-content bg-purple">昵称:{{formDetail.user.nickname | filterNickname}}</div></el-col>
                    <el-col :span="6"><div class="grid-content bg-purple">手机号:{{formDetail.user.phone}}</div></el-col>
                </el-row>
                <el-row :gutter="20" style="margin-top: 20px">
                    <el-col :span="6"><div class="grid-content bg-purple">锁定:{{formDetail.user.status | filterUserSta}}</div></el-col>
                    <el-col :span="6"><div class="grid-content bg-purple">会员:{{formDetail.user.user_level}}</div></el-col>
                    <el-col :span="6"><div class="grid-content bg-purple">会员到期时间:{{formDetail.user.user_level_expire}}</div></el-col>
                    <el-col :span="6"><div class="grid-content bg-purple">注册时间:{{formDetail.user.created_time}}</div></el-col>
                </el-row>
           </div>
            <div>
                <el-tabs v-model="activeName" @tab-click="handleClick">
                    <el-tab-pane label="课程订阅" name="first">
                        <el-table :data="firsttab" border max-height="250">
                            <el-table-column label="课程标题" align="center">
                                <template slot-scope="scope">
                                    {{ scope.row.title }}
                                    </template>
                            </el-table-column>
                            <el-table-column label="购买价格" align="center">
                                <template slot-scope="scope">
                                    {{ scope.row.price }}
                                </template>
                            </el-table-column>
                            <el-table-column label="购买时间" align="center">
                                <template slot-scope="scope">
                                    {{ scope.row.created_time }}
                                </template>
                            </el-table-column>
                        </el-table>
                    </el-tab-pane>
                    <el-tab-pane label="专栏订阅" name="second">
                         <el-table :data="secondtab" border max-height="250">
                            <el-table-column label="专栏标题" align="center">
                                <template slot-scope="scope">
                                    {{ scope.row.title }}
                                    </template>
                            </el-table-column>
                            <el-table-column label="购买价格" align="center">
                                <template slot-scope="scope">
                                    {{ scope.row.price }}
                                </template>
                            </el-table-column>
                            <el-table-column label="购买时间" align="center">
                                <template slot-scope="scope">
                                    {{ scope.row.created_time }}
                                </template>
                            </el-table-column>
                        </el-table>
                    </el-tab-pane>
                    <el-tab-pane label="订单记录" name="third">
                        <el-table :data="thirdtab" border max-height="250">
                            <el-table-column label="ID" align="center">
                                <template slot-scope="scope">
                                    {{ scope.row.id }}
                                    </template>
                            </el-table-column>
                            <el-table-column label="订单号" align="center">
                                <template slot-scope="scope">
                                    {{ scope.row.no }}
                                    </template>
                            </el-table-column>
                            <el-table-column label="购买价格" align="center">
                                <template slot-scope="scope">
                                    {{ scope.row.price }}
                                </template>
                            </el-table-column>
                            <el-table-column label="状态" align="center">
                                <template slot-scope="scope">
                                    {{ scope.row.status | filterThirdSta }}
                                </template>
                            </el-table-column>
                            <el-table-column label="商品" align="center">
                                <template slot-scope="scope">
                                    {{ scope.row.title }}
                                </template>
                            </el-table-column>
                            <el-table-column label="购买时间" align="center">
                                <template slot-scope="scope">
                                    {{ scope.row.created_time }}
                                </template>
                            </el-table-column>
                        </el-table>
                    </el-tab-pane>
                    <el-tab-pane label="观看历史" name="fourth">
                         <el-table :data="fourthtab" border max-height="250">
                            <el-table-column label="课程标题" align="center">
                                <template slot-scope="scope">
                                    {{ scope.row.title }}
                                    </template>
                            </el-table-column>
                            <el-table-column label="课程类型" align="center">
                                <template slot-scope="scope">
                                    {{ scope.row.type | filterFourthType }}
                                </template>
                            </el-table-column>
                            <el-table-column label="学习时长" align="center">
                                <template slot-scope="scope">
                                    {{ scope.row.total_time }}
                                </template>
                            </el-table-column>
                        </el-table>
                    </el-tab-pane>
                    <el-tab-pane label="用户评论" name="fifth">
                         <el-table :data="fifthTab" border max-height="250">
                            <el-table-column label="评论内容" align="center">
                                <template slot-scope="scope">
                                    {{ scope.row.content }}
                                    </template>
                            </el-table-column>
                            <el-table-column label="评论时间" align="center">
                                <template slot-scope="scope">
                                    {{ scope.row.created_time }}
                                </template>
                            </el-table-column>
                             <el-table-column label="课程标题" align="center">
                                <template slot-scope="scope">
                                    {{ scope.row.title }}
                                </template>
                            </el-table-column>
                            <el-table-column label="类型" align="center">
                                <template slot-scope="scope">
                                    {{ scope.row.type | filterFourthType }}
                                </template>
                            </el-table-column>
                        </el-table>
                    </el-tab-pane>
                </el-tabs>

                <div class="block">
                    <el-pagination
                        background
                        @size-change="handleSizeChange2"
                        @current-change="handleCurrentChange2"
                        :current-page="list.page"
                        :page-sizes="[20, 30, 40, 50]"
                        :page-size="list.limit"
                        layout="total, sizes, prev, pager, next, jumper"
                        :total="total"
                    >
                    </el-pagination>
                </div>
            </div>
        </el-dialog>


        <div class="block">
            <el-pagination
                background
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="tabList.page"
                :page-sizes="[20, 30, 40, 50]"
                :page-size="tabList.limit"
                layout="total, sizes, prev, pager, next, jumper"
                :total="total2"
            >
            </el-pagination>
        </div>
    </div>
</template>

<script>

// import { fetchList, updateMedia } from '../../api/media'
import { fetchList, fetchUserCourse, fetchUserColumn, fetchUserOrder, fetchUserHistory, fetchUserComment } from '../../api/user'
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
            value:null,
            total:null,
            list: [],
            listQuery: Object.assign({}, listQuery),
            dialogFormVisible: false,
            form: Object.assign({}, formDefault),
            isEdit: false,
            currentindex:null,
            curreneSelect: [],
            formDetail:{
                created_time: "",
                id: null,
                no_access: null,
                no_comment: null,
                total_consume: null,
                updated_time: "",
                user: {
                    avatar: "",
                    created_time: "",
                    id: null,
                    nickname: "",
                    phone: "15108544507",
                    status: null,
                    user_level: "",
                    user_level_expire: "2021-04-21 08:47:22",
                    username: ""
                }
            },
            // tab选项卡中的东西
            tabList:{
                page:1,
                limit:20
            },
            total2:null,
            activeName: 'first',
            curentPanName:'first',
            firsttab:[],
            secondtab:[],
            thirdtab:[],
            fourthtab:[],
            fifthTab:[],


        }
    },

    mounted () {
        this.getUser()
        this.getFirstTab()
    },

    methods:{
        // 获取数据
        getUser() {
            fetchList(this.listQuery).then(res => {
                console.log(res)
                if(res.code == 20000) {
                    this.list = res.data.items
                    this.total = res.data.total
                }
            })
        },

        search () {
            console.log(this.listQuery)
            fetchList(this.listQuery).then(res => {
                console.log(res)
                this.list = res.data.items
                this.total = res.data.total
            })
            // fetchList(this.listQuery).then(res => {
            //     console.log(res)
            // })
        },

        handleCommand (command) {
            this.$confirm('此操作将改变评论/访问, 是否继续?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
                }).then(() => {
                    if(this.currentindex) {
                        if(command == 'no_comment') {
                            this.list[this.currentindex].no_comment = 0
                        } else if(command == 'no_access') {
                            this.list[this.currentindex].no_access = 0
                        }
                    } else {
                        if(this.curreneSelect.length < 1) {
                            this.$message.info('至少选择一项数据')
                            return
                        }
                        if(command == 'no_comment') {
                            this.curreneSelect.forEach(item => {
                                item.no_comment = 0
                            })
                        } else if(command == 'no_access') {
                              this.curreneSelect.forEach(item => {
                                item.no_access = 0
                            })
                        }   
                    }
                  
                    console.log(this.list[this.currentindex])
                    let msg = command == 'no_comment' ? '禁止评论成功':'禁止访问成功'
                    this.currentindex = null
                    this.curreneSelect = []
                    this.$message({
                        type: 'success',
                        message: msg
                    });
            }).catch(() => {
                this.$message({
                type: 'info',
                message: '已取消删除'
            });          
            });
        },

        handleSelectionChange (val) {
            this.curreneSelect = val
        },

      

        getcurrentIndex (index) {
            console.log(index)
            this.currentindex = index
        },

        editData (index, row) {
            this.formDetail = this.list[index]
            this.dialogFormVisible = true
            this.activeName = 'first'
        },
        // 每页条数改变的时候触发
        handleSizeChange (val) {
            this.listQuery.page = 1
            this.listQuery.limit = val
            this.getUser()
        },
        // page改变的时候触发
        handleCurrentChange (val) {
            this.listQuery.page = val
            this.getUser()
        },



        // 每页条数改变的时候触发
        handleSizeChange2 (val) {
            this.tabList.page = 1
            this.tabList.limit = val
            if(this.curentPanName == 'first') {
                this.getFirstTab()
            } else if(this.curentPanName == 'second') {
                this.getSecondTab()
            } else if(this.curentPanName == 'third') {
                this.getThirdTab()
            } else if(this.curentPanName == 'fourth') {
                this.getFourthTab()
            } else if(this.curentPanName == 'fifth') {
                this.getFifthTab()
            }
        },
        // page改变的时候触发
        handleCurrentChange2 (val) {
            this.tabList.page = val
            if(this.curentPanName == 'first') {
                this.getFirstTab()
            } else if(this.curentPanName == 'second') {
                this.getSecondTab()
            } else if(this.curentPanName == 'third') {
                this.getThirdTab()
            } else if(this.curentPanName == 'fourth') {
                this.getFourthTab()
            } else if(this.curentPanName == 'fifth') {
                this.getFifthTab()
            }
        },

        handleClick(tab, event) {
            this.curentPanName = tab.paneName
           
            this.getTablist(this.curentPanName)
        },

        getFirstTab () {
            fetchUserCourse (this.tabList).then(res => {
                // console.log(res)
                this.firsttab =res.data.items
                this.total2 = res.data.total
            })
        },   
        getSecondTab () {
            fetchUserColumn (this.tabList).then(res => {
                this.secondtab =res.data.items
                this.total2 = res.data.total
            })
        },   

        getThirdTab () {
             fetchUserOrder (this.tabList).then(res => {
                 console.log(res)
                this.thirdtab =res.data.items
                this.total2 = res.data.total
            })
        },

        getFourthTab () {
             fetchUserHistory (this.tabList).then(res => {
                console.log(res)
                this.fourthtab =res.data.items
                this.total2 = res.data.total
            })
        },

        getFifthTab () {
             fetchUserComment (this.tabList).then(res => {
                console.log(res)
                this.fifthTab =res.data.items
                this.total2 = res.data.total
            })
        },



        getTablist (paneName) {
            if(paneName == 'first') {
                this.getFirstTab()
            } else if(paneName == 'second') {
                this.getSecondTab()
            } else if(paneName == 'third') {
                this.getThirdTab()
            } else if(paneName == 'fourth') {
                this.getFourthTab()
            } else if(paneName == 'fifth') {
                this.getFifthTab()
            }
        },
      
    },

    filters: {
        
        filterStatus (value) {
            if(value == 1) {
                return '已上架'
            } else {
                return '已下架'
            }
        },

        filterNickname (val) {
            if(!val) {
                return '未设置'
            } else {
                return val
            }
        },

        filterUserSta (val) {
            if(val == 0) {
                return '未锁定'
            } else if(val == 1) {
                return '已锁定'
            }
        },
        filterThirdSta (val) {
            if(val == 'success') {
                return '成功'
            } else if(val == 'fail'){
                return '失败'
            } else {
                return '待支付'
            }
        },

        filterFourthType (val) {
            if(val == 'audio') {
                return '音频'
            } else if(val == 'video'){
                return '视频'
            } else if(val == 'media'){
                return '图文'
            } else if(val == 'column'){
                return '专栏'
            }
        },
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
.block {
    margin-top: 20px;
}
.auto {
    scroll-behavior: auto;
}
</style>