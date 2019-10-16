<template>
    <div class="container">
        <el-row type="flex" justify="space-between">
            <!-- 订单表单 -->
            <div class="main">
                <OrderForm :insurances="insurances"/>
            </div>

            <!-- 侧边栏 -->
            <div class="aside">
                          
            </div>
        </el-row>
    </div>
</template>

<script>
// 引入订创建订单的表单组件
import OrderForm from "@/components/air/orderForm";

export default {
    // 注册
    components:{
        OrderForm
    },

    // 数据
    data(){
        return {
            insurances: []
        }
    },

    // 页面加载完毕时执行
    mounted(){
        // 从url中获取航班id和座位seat_xid
        const query = this.$route.query;
        
        // 请求航班信息
        this.$axios({
            url: "/airs/" + query.id,
            params: {
                seat_xid: query.seat_xid
            }
        }).then(res=>{
            console.log(res)
            const { status, data } = res;

            if(status === 200) {
                // 将获取到的保险信息数组赋值给data中的insurances
                this.insurances = data.insurances;
            }
        })
    }

    
}
</script>

<style lang="less" scoped>
    .container{
        width:1000px;
        margin:20px auto;
    }
    
    /*aside*/
    .aside{
        width: 350px;
        height: fit-content;
        border:1px #ddd solid;
    }
</style>