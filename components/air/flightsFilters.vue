<template>
    <div class="filters">
        <el-row type="flex" class="filters-top" justify="space-between" align="middle">
            <el-col :span="8">
                单程： 
                {{data.info.departCity}} - {{data.info.destCity}} 
                / 
                {{data.info.departDate}}
            </el-col>
            <el-col :span="4">
                <el-select size="mini" v-model="airport" placeholder="起飞机场" @change="handleAirport">
                    <el-option
                    v-for="(item,index) in data.options.airport"
                    :key="index"
                    :label="item"
                    :value="item"
                    >
                    </el-option>
                </el-select>
            </el-col>
            <el-col :span="4">
                <el-select size="mini" v-model="flightTimes"  placeholder="起飞时间" @change="handleFlightTimes">
                    <el-option
                    v-for="(item,index) in data.options.flightTimes"
                    :key="index"
                    :label="`${item.from}:00 - ${item.to}:00`"
                    :value="`${item.from},${item.to}`"
                    >
                    </el-option>
                </el-select>
            </el-col>
            <el-col :span="4">
                <el-select size="mini" v-model="company"  placeholder="航空公司" @change="handleCompany">
                    <el-option
                    v-for="(item,index) in data.options.company"
                    :key="index"
                    :label="item"
                    :value="item">
                    </el-option>
                </el-select>
            </el-col>
            <el-col :span="4">
                <el-select size="mini" v-model="airSize" placeholder="机型" @change="handleAirSize">
                    <el-option
                    v-for="(item,index) in sizeOptions"
                    :key="index"
                    :label="item.name"
                    :value="item.size">
                    </el-option>
                </el-select>
            </el-col>
        </el-row>
        <div class="filter-cancel">
            筛选：
            <el-button 
                       type="primary" 
                       round 
                       plain 
                       size="mini" 
                       @click="handleFiltersCancel">
                撤销
    		</el-button>
        </div>
    </div>
</template>

<script>
export default {

    props:{
        data:{
            type: Object,

            default: {

            }
        }
    },

    data(){
        return {
            airport: "",        // 机场
            flightTimes: "",    // 出发时间
            company: "",        // 航空公司
            airSize: "",        // 机型大小
            sizeOptions: [
                {name: "大", size: "L"},
                {name: "中", size: "M"},
                {name: "小", size: "S"},
            ],
        }
    },
    methods: {
        // 选择机场时候触发
        handleAirport(value){
            // 通过filter方法筛选数组中出发机场和选中的出发机场名字相同的数据
            // 这些数据是一个数组
            const arr = this.data.flights.filter(v=>{
                return v.org_airport_name === value;
            });

            // 调用emit方法出发父组件传过来的方法，将arr的值返回父组件
            this.$emit("handleFilter",arr);
        },

        // 选择出发时间时候触发
        handleFlightTimes(value){
            // 将出发时间分割成数组
            const [from,to] = value.split(",");
            
            // 通过filter方法筛选数组中出发时间段和选中的出发时间相同的数据
            const arr = this.data.flights.filter(v=>{
                // 截取出发时间的小时部分
                const start = +v.dep_time.split(":")[0];
                return start>from && start<to;
            });
            
            this.$emit("handleFilter",arr);
        },

         // 选择航空公司时候触发
        handleCompany(value){
            // 遍历数组，找到与vlaue相同的项
            const arr = this.data.flights.filter(v=>{
                return v.airline_name === value;
            });

            this.$emit("handleFilter",arr);
        },

         // 选择机型时候触发
        handleAirSize(value){
           // 遍历数组，找到与vlaue相同的项
            const arr = this.data.flights.filter(v=>{
                return v.plane_size === value;
            });

            this.$emit("handleFilter",arr);
        },
        
        // 撤销条件时候触发
        handleFiltersCancel(){
            airport: ""    
            flightTimes: ""
            company: ""    
            airSize: ""    
        },
    }
}
</script>

<style scoped lang="less">
    .filters{
        margin-bottom:20px;
    }

    .filters-top{
        > div{
            /deep/ .el-select{
                margin-left:10px;
            }
        }
    }

    .filter-cancel{
        margin-top:10px;
    }
</style>