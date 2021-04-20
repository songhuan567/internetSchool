<template>
    <div class="wrapper">

        <div class="search">
            <div>
                <el-button type="primary"><i class="el-icon-edit" />新增图文</el-button>
            </div>
            <div class="right">
                  <el-select v-model="value" placeholder="商品状态">
                    <el-option
                        v-for="item in options"
                        :key="item.value"
                        :label="item.label"
                        :value="item.value"
                    >
                    </el-option>
                </el-select>
                <el-input placeholder="标题"></el-input>
                <el-button type="primary"><i class="el-icon-search" />搜索</el-button>
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
                    <el-tag type="danger"> {{ scope.row.sub_count | filterStatus }}</el-tag>
                </template>
            </el-table-column>
            <el-table-column label="创建时间" align="center" width="200">
                <template slot-scope="scope">
                    {{ scope.row.created_time }}
                </template>
            </el-table-column>
            <el-table-column label="操作" align="center" width="300">    
               <el-button size="mini" type="primary">编辑</el-button>
               <el-button size="mini" >下架</el-button>
               <el-button size="mini" type="danger">删除</el-button>
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

import {fetchList} from '../../api/media'


const listQuery = {
    page:1,
    limit: 20,
    title: '',
    sort: false,
    status: null,
}

export default {
    data () {
        return {
            options:[
                {value: 1, label: '已下架'},
                {value: 2, label: '已上架'},
            ],
            value: '',
            total:null,
            list: [],
            listQuery: Object.assign({}, listQuery),
        }
    },

    mounted () {
        this.getMedia()
    },

    methods:{
        getMedia() {
            fetchList(this.listQuery).then(res => {
                console.log(res)
                if(res.code == 20000) {
                    this.list = res.data.items
                    this.total = res.data.total
                }
            })
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
</style>