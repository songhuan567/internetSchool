<template>
    <div class="wrapper">
        <el-row :gutter="20">
            <el-col :span="6">
                <el-card class="left" style="height: 80vh;">
                     <div slot="header" class="clearfix">
                        <span>组件列表</span><br>
                        <span style="font-size: 12px; color: #bbb">点击组件添加至页面</span>
                    </div>
                    <div>
                        <el-col :span="12" v-for="(item, index) in menuList" :key="index">
                            <el-card shadow="hover" :body-style="{ padding: '15px 8px','cursor':'pointer' }">
                                <div style="display: flex;align-items: center;">
                                    <i :class="item.icon" style="font-size: 25px; color: #13ce66;margin-right:8px"></i>
                                    <span style="font-size: 13px">{{ item.title }}</span>
                                </div>
                            </el-card>
                        </el-col>
                    </div>
                </el-card>
            </el-col>
            <el-col :span="8">
                <el-card class="center" style="height: 80vh; overflow: inherit;" :body-style="{padding:'10px 8px'}">

                    <img src="./status.png" alt="" style="width: 100%;margin-bottom: -10px">

                    <div style="margin-left: -10px;margin-right: -10px;">
                        <div class="checked-box" v-for="(item, index) in templates" :key="index"
                            :class="item.checked ? 'checked-box-active':''"
                            @click="handleCheckedComponent(item)"
                        >
                              <!-- 操作按钮 -->
                            <div v-if="item.checked" class="checked-box-btns">
                                <i class="el-icon-top"
                                    :class="index == 0 ? 'i-disabled':''"
                                    @click.stop="moveUp(index)"
                                    ></i>
                                <i class="el-icon-bottom"
                                    :class="(index + 1) == templates.length ? 'i-disabled':''"
                                    @click.stop="moveDown(index)"
                                ></i>
                                <i class="el-icon-delete" @click.stop="deleteComponent(index)"></i>
                            </div>
                            <!-- 组件 -->
                            <div style="height: 100px;border: 1px solid;display: flex;align-items: center;justify-content: center;">
                                {{item.id}}
                            </div>
                        </div>
                    </div>
                </el-card>
            </el-col>
            <el-col :span="10" v-show="false">
                <el-card class="right">333</el-card>
            </el-col>
        </el-row>
    </div>

</template>

<script>
import util from '@/utils/util.js'

export default {
    data () {
        return {
            menuList: [{
                icon: "el-icon-s-order",
                title: "课程列表",
                type: "list",
                default: {}
            }, {
                icon: "el-icon-search",
                title: "搜索框",
                type: "list",
                default: {}
            }],

             templates: [{
                    id: 1,
                    checked: false
                }, {
                    id: 2,
                    checked: false
                }, {
                    id: 3,
                    checked: false
                }, {
                    id: 4,
                    checked: false
                }]
        }
    },
    methods:{
        deleteComponent(index) {
            this.$confirm('是否要删除该组件?', '提示', {
                confirmButtonText: '删除',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                this.templates.splice(index, 1)
                this.$message({
                    type: 'success',
                    message: '删除成功!'
                });
            })
        },

        handleCheckedComponent(row) {
            this.templates.map(v => {
                v.checked = false
                return v
            })

            row.checked = true
        },

        moveUp(index) {
            if (index == 0) {
                return
            }
            util.moveUp(this.templates, index)
        },
        moveDown(index) {
            if (index == (this.templates.length - 1)) {
                return
            }
            util.moveDown(this.templates, index)
        }
    }
}
</script>

<style scoped>
.wrapper {
    padding: 20px;
    background-color: #eee;
}
.left {
    background-color: #fff;
    height: 500px;
}
.right {
    background-color: #fff;
}
.center {
    background-color: #fff;
}

.checked-box {
    cursor: pointer;
    position: relative;
}

.checked-box-active {
    border: 2px dotted #2196f3;
    padding: 5px 0;
    margin-bottom: 10px;
}

.checked-box-btns {
    position: absolute;
    display: flex;
    flex-direction: column;
    background-color: #ffffff;
    border: 2px dotted #2196f3;
    right: -29px;
    top: -2px;
    z-index: 999;
    border-left-width: 0;
}

.checked-box-btns i {
    padding: 4px 6px;
    color: #2196f3;
    font-weight: bold;
}

.checked-box-btns i:hover {
    background-color: #eeeeee;
}

.checked-box-btns .i-disabled {
    background-color: #ffffff;
    cursor: no-drop;
    color: #bbbbbb;
}

</style>