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
                    v-for="(item,index) in pageDate"
                    :key="index"
                    :item="item"
                />

                 <!-- 分页组件 -->
                 <!-- 
                    size-change：选择每页显示的数据时会触发，如从每页显示5条切换到每页显示10条
                    current-change：当前显示页面改变时触发，如从第1页切换到第2页
                    current-page：当前页码，也就是pageIndex
                    page-size：每页现实的条数，也就是pageSize
                    page-sizes：每页显示条数选择器的选项设置
                    total：总的数据条数
                  -->
                <el-pagination
                    @size-change="handleSizeChange"
                    @current-change="handleCurrentChange"
                    :current-page="pageIndex"
                    :page-sizes="[5, 10, 15, 20]"
                    :page-size="pageSize"
                    layout="total, sizes, prev, pager, next, jumper"
                    :total="flightsData.total">
                </el-pagination>

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
            flightsData:{},

            // 分页使用的数据数组
            pageDate:[],
            // 当前页码
            pageIndex: 1,
            // 当前页面显示条数
            pageSize: 5,
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

                // 获取分页的第一页数据
                this.pageDate = this.flightsData.flights.slice(0,5);
            }
        })
    },

    // 方法
    methods:{
        // 选择每页显示的数据时会触发
        handleSizeChange(val) {
            // 将分页组件选择的条数同步到pageSize
            this.pageSize = val; 
            // 获取对应页面数据
            this.pageDate = this.flightsData.flights.slice(
                (this.pageIndex-1) * this.pageSize,
                this.pageIndex * this.pageSize
            )

        },
        // 当前显示页码改变时触发
        handleCurrentChange(val) {
            this.pageIndex = val;
            // 获取对应页面数据
            this.pageDate = this.flightsData.flights.slice(
                (this.pageIndex-1) * this.pageSize,
                this.pageIndex * this.pageSize
            )
        }
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