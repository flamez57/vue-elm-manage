<template>
    <div class="fillcontain">
        <head-top></head-top>
        <div class="table_container">
            <el-table
		      :data="tableData"
              highlight-current-row
              :row-class-name="first"
		      style="width: 100%">
		      <el-table-column
		        prop="user_name"
		        label="姓名"
		        width="180">
		      </el-table-column>
		      <el-table-column
		        prop="create_time"
		        label="注册日期"
		        width="220">
		      </el-table-column>
              <el-table-column
                prop="city"
                label="地址"
                width="180">
              </el-table-column>
		      <el-table-column
		        prop="admin"
		        label="权限">
		      </el-table-column>
		    </el-table>
		    <div class="Pagination" style="text-align: right;margin-top: 10px;">
                <el-pagination
                  @size-change="handleSizeChange"
                  @current-change="handleCurrentChange"
                  :current-page="currentPage"
                  :page-sizes="[20,50,100,150]"
                  :page-size="20"
                  layout="total,sizes, prev, pager, next,jumper"
                  :total="count">
                </el-pagination>
            </div>
        </div>
    </div>
</template>

<script>
    import headTop from '../components/headTop'
    import {adminList, adminCount} from '@/api/getData'
    export default {
        data(){
            return {
                tableData: [],
                currentRow: null,
                offset: 0,
                limit: 20,
                count: 0,
                currentPage: 1,
            }
        },
    	components: {
    		headTop,
    	},
        created(){
            this.initData();
        },
       
        methods: {
            first(row,index){
                if(index===0){
                    return 'positive-row'
                }
            },
            async initData(){
                try{
                    const countData = await adminCount();
                    if (countData.status == 1) {
                        this.count = countData.count;
                    }else{
                        throw new Error('获取数据失败');
                    }
                    this.getAdmin();
                }catch(err){
                    console.log('获取数据失败', err);
                }
            },
            handleSizeChange(val) {
                this.limit = val;
                this.handleCurrentChange(1)
                //this.getAdmin();
            },
            handleCurrentChange(val) {
                this.currentPage = val;
                this.offset = (val - 1)*this.limit;
                this.getAdmin()
            },
            async getAdmin(){
                try{
                    const res = await adminList({offset: this.offset, limit: this.limit});
                    if (res.status == 1) {
                    	this.tableData = [];
                    	// res.data.forEach(item => {
                    	// 	const tableItem = {
                    	// 		create_time: item.create_time,
						//         user_name: item.user_name,
						//         admin: item.admin,
                        //         city: item.city,
                    	// 	}
                    	// 	this.tableData.push(tableItem)
                    	// })
                        this.tableData.push(...res.data)
                    }else{
                    	throw new Error(res.message)
                    }
                }catch(err){
                    console.log('获取数据失败', err);
                }
            }
        },
    }
</script>

<style lang="less">
	@import '../style/mixin';
    .table_container{
        padding: 20px;
    }
    .el-table .positive-row {
    background: #e2f0e4;
  }
</style>


