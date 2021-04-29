<template>
    <el-form ref="form" label-width="80px" size="normal">
        <el-form-item v-if="formType == 'search'" label="占位提示">
            <el-input v-model="search.placeholder" size="mini" style="width:200px;" @input="handleChange('placeholder')"></el-input>
        </el-form-item>
    </el-form>
</template>
<script>
export default {
    props:{
        formType:{
            type:String,
            default:""
        }
    },
    data() {
        return {
            search:{
                placeholder:""
            },
        }
    },
    methods:{
        // 监听实时变化
        handleChange(key){
            this.$emit('change',{
                key,
                value:this[this.formType][key]
            })
        },
        // 初始化数据
        initVal(val){
            for(let k in val){
                this[val.type][k] = val[k]
            } 
        }
    }
}
</script>
<style>
    
</style>