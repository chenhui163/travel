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
                <el-select size="mini" v-model="conditions.airport.value" placeholder="起飞机场" @change="handleAirport">
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
                <el-select size="mini" v-model="conditions.flightTimes.value"  placeholder="起飞时间" @change="handleFlightTimes">
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
                <el-select size="mini" v-model="conditions.company.value"  placeholder="航空公司" @change="handleCompany">
                    <el-option
                    v-for="(item,index) in data.options.company"
                    :key="index"
                    :label="item"
                    :value="item">
                    </el-option>
                </el-select>
            </el-col>
            <el-col :span="4">
                <el-select size="mini" v-model="conditions.airSize.value" placeholder="机型" @change="handleAirSize">
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
            // 机型列表
            sizeOptions: [
                {name: "大", size: "L"},
                {name: "中", size: "M"},
                {name: "小", size: "S"},
            ],

            // 声明一个对象，用于存放筛选的条件数据
            conditions : {
                airport: { name: "org_airport_name", value: "" }, // 出发机场
                flightTimes: { name: "dep_time", value: "" },      // 出发时间
                company: { name: "airline_name", value: ""},      // 选择的航空公司
                airSize: { name: "plane_size", value: ""},        // 选择的飞机型号
            },
        }
    },

    // 监听
    watch:{
        conditions:{ //监听的对象
            deep:true, //深度监听设置为 true
            handler:function(){
                // 声明空数组存储筛选结果
                let arr = [];

                // 遍历总数据的航班数组，从中找出符合筛选条件的
                this.data.flights.forEach( item => {
                    // 每遍历一个项都重新定义开关，初始为true
                    let flag = true;
                    // 遍历筛选条件对象的属性名组成的数组
                    Object.keys(this.conditions).forEach( v => {
                        // （不符合条件）如果属性数组中某个属性无值，直接中断进行下一次循环
                        if(!this.conditions[v].value){
                            return ;
                        }
                        // （不符合条件）如果起飞行时间段属性有值，对其进行处理后，判断是否符合筛选条件
                        else if(v==="flightTimes"){
                            // 对筛选条件中的起飞行时间段进行切割，得到一个数组
                            const filterTime = this.conditions[v].value.split(",");
                            // 获取总数据中的每一项的起飞时间切割后的起飞时间
                            let start = item[this.conditions[v].name].split(":")[0];
                            // 如果起飞时间不在筛选时间段内，改变开关为false
                            if(start< +filterTime[0] || start > +filterTime[1]){
                                flag = false;
                            }
                        }

                        // （过滤）将conditions中没有的属性都过滤掉，一遇到就改变flag为false
                        else if(item[this.conditions[v].name] !== this.conditions[v].value){
                            flag = false;
                        }
                    });

                    // （符合条件）经过上述筛选之后，若是flag没有改变，就说明是符合所有筛选条件的，追加到数组
                    if(flag){
                        arr.push(item);
                    }
                });

                // 将数据传回父组件
                this.$emit("handleFilter", arr);
            
            }
        }
    },

    methods: {
        // 选择机场时候触发
        handleAirport(value){
            this.conditions.airport.value = value;
        },

        // 选择出发时间时候触发
        handleFlightTimes(value){
            this.conditions.flightTimes.value = value;
        },

         // 选择航空公司时候出发
        handleCompany(value){
            this.conditions.company.value = value;
        },

         // 选择机型时候触发
        handleAirSize(value){
            this.conditions.airSize.value = value;
        },
        
        // 撤销条件时候触发
        handleFiltersCancel(){
            // 清空所有筛选条件
            this.conditions.airport.value = "";
            this.conditions.flightTimes.value = "";
            this.conditions.company.value = "";
            this.conditions.airSize.value = "";

            // 出发事件，恢复备份信息
            this.$emit("handleFilter");
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