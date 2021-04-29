<template>
    <el-card shadow="never">
         <div slot="header" class="clearfix">
            <span>{{ title }}</span>
            <el-button v-if="showMore" style="float: right; padding: 3px 0" type="text">查看全部</el-button>
        </div>
        <div>
           <div v-if="listData.length < 1" class="component-no-data" >【列表模块】请点击关联数据</div>
            <el-row v-else :gutter="20">
                <el-col v-for="(item, index) in list" :key="index" :span="listType == 'one' ? 24 : 12"
                    :offset="0" :style="listType == 'one' ? 'display: flex;margin-bottom: 10px;' : ''"
                >
                    <img :src="item.cover" alt="" style="width: 130px;margin-right: 10px;">
                    <div>
                        <div class="list-title">{{ item.title }}</div>
                        <div class="list-desc">{{ item.try }}</div>
                        <div class="list-price">
                            <span>￥{{item.price}}</span>
                            <span>￥{{item.t_price}}</span>
                        </div>
                    </div>
                </el-col>
            </el-row>
        </div>
    </el-card>
</template>

<script>
export default {
    props: {
        title: {
            type: String,
            default: ""
        },
        showMore: {
            type: Boolean,
            default: true
        },
        list: {
            type:Array,
            // default:[]    
        },
        listType: {
            type: String,
            default: "one"
        },
    },

    data () {
        return {
            listData: this.list ? this.list : []
        }
    }
}
</script>

<style scoped>

.component-no-data{
    background-color: #eee; 
    color: #ccc; 
    font-size: 16px; 
    padding: 15px;
    text-align: center
}
.list-card-header {
        border-bottom: 0;
    }
    
    .list-card-more {
        float: right;
        padding: 0;
        color: #999999;
    }
    
    .list-title {
        font-size: 14px;
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
        margin: 5px 0;
    }
    
    .list-desc {
        font-size: 12px;
        color: #999999;
        margin: 5px 0;
    }
    
    .list-price {
        display: flex;
        align-items: flex-end;
    }
    
    .list-price span:first-child {
        color: red;
    }
    
    .list-price span:last-child {
        color: #888888;
        margin-left: 5px;
        font-size: 10px;
    }
</style>