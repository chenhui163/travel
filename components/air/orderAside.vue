<template>
    <div class="aside">
        <div class="air-info">
            <el-row type="flex" justify="space-between" class="info-top">
                <div>{{flightsData.dep_date}}</div>
                <div>{{flightsData.org_city_name}} - {{flightsData.dst_city_name}}</div>
            </el-row>    
            <el-row 
            type="flex" 
            justify="space-between" 
            align="middle" 
            class="info-step">
                <el-col :span="5" class="flight-airport">
                    <strong>{{flightsData.dep_time}}</strong>
                    <span>{{flightsData.org_airport_name}}{{flightsData.org_airport_quay}}</span>
                </el-col>
                <el-col :span="14" class="flight-time">
                    <span>--- {{this.rankTime}} ---</span>
                    <span>{{flightsData.airline_name}}{{flightsData.flight_no}}</span>
                </el-col>
                <el-col :span="5" class="flight-airport">
                    <strong>{{flightsData.arr_time}}</strong>
                    <span>{{flightsData.dst_airport_name}}{{flightsData.dst_airport_quay}}</span>
                </el-col>
            </el-row> 
        </div>
        <el-row type="flex" justify="space-between" class="info-bar">
            <span>订单总价</span>
            <span>金额</span>
            <span>数量</span>
        </el-row>
        <el-row type="flex" justify="space-between" class="info-bar">
            <span>成人机票</span>
            <span>￥{{flightsData.seat_infos.org_settle_price}}</span>
            <span>x{{count}}</span>
        </el-row>
        <el-row type="flex" justify="space-between" class="info-bar">
            <span>机建＋燃油</span>
            <span>¥{{flightsData.airport_tax_audlet}}/人/单程</span>
            <span>x{{count}}</span>
        </el-row>
        <el-row type="flex" justify="space-between" align="middle" class="info-bar">
            <span>应付总额：</span>
            <span class="price">￥ {{allPrice}}</span>
        </el-row>           
    </div>
</template>

<script>
export default {
   
    props: {
        flightsData: {
            type: Object,
            default(){
                return {
                    
                }
            }
        },
        //订单总价格
        allPrice: 0,
        // 订单总人数
        count: 0
    },

    // 计算
    computed: {
        // 计算飞行时间
        rankTime(){
            // 如果数据还没传过来，就不计算
            if(!this.flightsData.dep_time){
                return "";
            }

            // 1.将出发时间和到达时间转化为分钟
            // 1.1转成数组
            const dep = this.flightsData.dep_time.split(":");
            const arr = this.flightsData.arr_time.split(":");

            // 1.2转成分钟
            const depMinuts = dep[0] * 60 + (+dep[1]);
            // 到达时间如果是小于出发时间，说明时第二天
            // 那么就给到达时间加上24小时
            if( (+arr[0]) < dep[0] ){
                arr[0] += 24;
            }
            const arrMinuts = arr[0] * 60 + (+arr[1]);
            
            // 2.计算相差分钟数
            const coustTime = arrMinuts - depMinuts;

            // 3.返回时间
            return `${Math.floor(coustTime / 60)}时${coustTime % 60}分`;
        }
    }
}
</script>

<style scoped lang="less">
/*aside*/
.aside{
    width: 350px;
    height: fit-content;
    border:1px #ddd solid;
}

.air-info{
    padding:15px;

    .info-top{
        margin-bottom:10px;
        > div:last-child{
            font-size:14px;
        }
    }

    .info-step{
        .flight-airport{
            strong{
                display: block;
                font-size: 22px;
                font-weight: normal;
            }

            span{
                font-size: 12px;
                color:#999;
            }
        }

        .flight-time{
            text-align: center;
            font-size: 12px;
            color:#999;
            span{
                display: block;
            }
        }
    }
}

.info-bar{
    border-top:1px #ddd dashed;
    padding: 10px 15px;
    font-size: 14px;
    color: #666;

    .price{
        font-size:28px;
        color: orange;
    }
}
</style>