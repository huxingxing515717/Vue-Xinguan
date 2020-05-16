<template>
    <div class="outStocks">
        <!-- 面包导航 -->
        <el-breadcrumb separator="/" style="padding-left:10px;padding-bottom:10px;font-size:12px;">
            <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>物资管理</el-breadcrumb-item>
            <el-breadcrumb-item>物资入库</el-breadcrumb-item>
        </el-breadcrumb>
        <!-- 卡片部分-->
        <el-card>
        <!--搜索部分-->
            <el-form :inline="true" :model="queryMap" class="demo-form-inline">
                <el-form-item label="发放单号">
                    <el-input v-model="queryMap.outNum" placeholder="发放单号"></el-input>
                </el-form-item>
                <el-form-item label="发放类型">
                    <el-select v-model="queryMap.type" placeholder="发放类型">
                        <el-option label="全部类型" value=""></el-option>
                        <el-option label="直接发放" value="0"></el-option>
                        <el-option label="审核发放" value="1"></el-option>
                    </el-select>
                </el-form-item>
                <el-form-item>
                    <el-button type="primary" @click="search">查询</el-button>
                </el-form-item>
            </el-form>
<!--            数据表格-->
                <el-table
                        border
                        :data="tableData"
                        style="width: 100%;height: 430px;">
                    <el-table-column label="#" prop="id" width="50"></el-table-column>
                    <el-table-column  prop="outNum" :show-overflow-tooltip='true' label="发放单号" width="180"></el-table-column>
                    <el-table-column prop="type" label="发放类型" >
                        <template slot-scope="scope">
                            <el-tag size="mini" effect="dark" type="success" v-if="scope.row.type===0">直接发放</el-tag>
                            <el-tag size="mini" effect="dark" v-else-if="scope.row.type===1">申请发放</el-tag>
                        </template>
                    </el-table-column>
                    <el-table-column prop="priority" label="紧急程度">
                        <template slot-scope="scope">
                            <el-rate
                                    :disabled="true"
                                    v-model="scope.row.priority"
                                    show-text
                                    :texts="['不急','常规','紧急','特急','超急']"
                            >
                            </el-rate>
                        </template>
                    </el-table-column>
                    <el-table-column prop="productNumber" label="总数" ></el-table-column>
                    <el-table-column prop="phone" label="联系方式" ></el-table-column>
                    <el-table-column prop="createTime" label="发放时间" ></el-table-column>
                    <el-table-column prop="status" label="状态" width="100">
                        <template slot-scope="scope">
                            <el-tag size="mini" type="danger" effect="plain" v-if="scope.row.status==1">回收</el-tag>
                            <el-tag size="mini" effect="plain" v-if="scope.row.status==0">已放</el-tag>
                            <el-tag size="mini" effect="plain" type="warning" v-if="scope.row.status==2">审核中</el-tag>
                        </template>
                    </el-table-column>

                </el-table>
<!--                表格分页-->
                <el-pagination
                        style="margin-top:20px;"
                        background
                        @size-change="handleSizeChange"
                        @current-change="handleCurrentChange"
                        :current-page="queryMap.pageNum"
                        :page-sizes="[6, 20, 30, 40]"
                        :page-size="queryMap.pageSize"
                        layout="total, sizes, prev, pager, next, jumper"
                        :total="total"
                ></el-pagination>



        </el-card>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                total:0,
                queryMap:{pageNum:1,pageSize:6},
                tableData:[],
            }
        },
        methods:{
            /**
             * 加载表格数据
             */
            async loadTableData() {
                const {data: res} = await this.$http.get("outStock/findOutStockList", {
                    params: this.queryMap
                });
                if (res.code !== 200) {
                    return this.$message.error("获取列表失败");
                } else {
                    this.total = res.data.total;
                    this.tableData = res.data.rows;
                }
            },
            /**
             * 改变页码
             */
            handleSizeChange(newSize) {
                this.queryMap.pageSize = newSize;
                this.loadTableData();
            },
            /**
             * 点击分页
             */
            handleCurrentChange(current) {
                this.queryMap.pageNum = current;
                this.loadTableData();
            },
            //搜索
            search() {
                this.queryMap.pageNum = 1;
                this.loadTableData();
            },

        },
        created() {
            this.loadTableData();
        }
    }
</script>
