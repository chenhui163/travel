<template>
    <section class="contianer">
        <el-row  type="flex" justify="space-between">
            
            <!-- 顶部过滤列表 -->
            <div class="flights-content">
                <!-- 过滤条件 -->
                <div>
                    
                </div>
                
                <!-- 航班头部布局 -->
                <FlightsListHead />
                
                
                <!-- 航班信息 -->
                <!-- 遍历航班列表数据，将航班列表数组每一项传递给航班信息组件 -->
                <FlightsItem 
                    v-for="(item,index) in flightsData.flights"
                    :key="index"
                    :item="item"
                />
            </div>

            <!-- 侧边栏 -->
            <div class="aside">
                <!-- 侧边栏组件 -->
            </div>
        </el-row>
    </section>
</template>

<script>

import moment from "moment";

// 引入航班列表头部组件
import FlightsListHead from "@/components/air/flightsListHead";
// 引入航班列表组件
import FlightsItem from "@/components/air/flightsItem";

export default {

    // 注册
    components:{
        FlightsListHead,
        FlightsItem
    },

    // 数据
    data(){
        return {
            // 航班列表的总数据
            flightsData:{}
        }
    },

    // 页面加载完毕时执行
    mounted(){
        // params：get方式传递url参数的方法
        // this.$route.query：获取url地址栏中的参数组成的对象
        this.$axios({
            url:"/airs",
            params: this.$route.query
        }).then(res=>{
            const {status, data} = res;
            // 如果请求成功，将数据赋值给航班列表总数据
            if(status===200){
                this.flightsData = data;
                // console.log(data.flights);
            }
        })
    }
}
</script>

<style scoped lang="less">
    .contianer{
        width:1000px;
        margin:20px auto;
    }

    .flights-content{
        width:745px;
        font-size:14px;
    }

    .aside{
        width:240px;
    } 
</style>