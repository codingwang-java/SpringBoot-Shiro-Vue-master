<template>
  <div class="app-container">
    <div class="filter-container">
      <el-form>
        <el-form-item>
          <el-button type="primary" icon="plus" @click="showCreate" v-if="hasPerm('article:add')">创建
          </el-button>
        </el-form-item>
      </el-form>
    </div>
    <el-button type="primary" @click="dialogTableVisible = true">社区实时空气质量</el-button>

    <el-dialog title="今日空气质量" :visible.sync="dialogTableVisible">
      <el-table :data="list">
        <el-table-column property="id" label="序号" width="100"></el-table-column>
        <el-table-column property="communityName" label="小区" width="100"></el-table-column>
        <el-table-column property="communityId" label="id"width="100"></el-table-column>
        <el-table-column property="airdate" label="日期" width="100"></el-table-column>
        <el-table-column property="hour" label="时间" width="100"></el-table-column>
        <el-table-column property="pm2_5" label="pm2.5"width="100"></el-table-column>
        <el-table-column property="pm10" label="pm10" width="100"></el-table-column>
        <el-table-column property="so2" label="二氧化硫" width="100"></el-table-column>
        <el-table-column property="no2" label="二氧化氮"width="100"></el-table-column>
        <el-table-column property="o3" label="臭氧" width="100"></el-table-column>
        <el-table-column property="co" label="一氧化碳" width="100"></el-table-column>
        <el-table-column property="address" label="地址"></el-table-column>
      </el-table>
    </el-dialog>
   <!-- <el-table :data="list" v-loading.body="listLoading" element-loading-text="拼命加载中" border fit
              highlight-current-row>
      <el-table-column align="center" label="序号" width="80">
        <template slot-scope="scope">
          <span v-text="getIndex(scope.$index)"> </span>
        </template>
      </el-table-column>
      <el-table-column align="center" prop="content" label="文章" style="width: 60px;"></el-table-column>
      <el-table-column align="center" label="时间" width="170">
        <template slot-scope="scope">
          <span>{{scope.row.createTime}}</span>
        </template>
      </el-table-column>
      <el-table-column align="center" label="管理" width="200" v-if="hasPerm('article:update')">
        <template slot-scope="scope">
          <el-button type="primary" icon="edit" @click="showUpdate(scope.$index)">修改</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-table :data="list" border style="width: 100%" height="500">
      <el-table-column align="center" label="序号" width="80">
        <template slot-scope="scope">
          <span v-text="getIndex(scope.$index)"> </span>
        </template>
      </el-table-column>
      <el-table-column algin="center" prop="communityName" label="小区" width="120">
      </el-table-column>
      <el-table-column prop="communityId" label="编号" width="120"></el-table-column>
      <el-table-column prop="mianji" label="面积" width="120"></el-table-column>
      <el-table-column prop="rongjilv" label="容积率" width="120"></el-table-column>
      <el-table-column prop="lvhualv" label="绿化率" width="120"></el-table-column>
      <el-table-column prop="house" label="房屋数量" width="120"></el-table-column>
      <el-table-column prop="wuyetel" label="物业电话" width="120"></el-table-column>
      <el-table-column prop="wuyeName" label="物业公司" width="120"></el-table-column>
      <el-table-column fixed="right" label="操作" width="100">
        <template slot-scope="scope">
          <el-button @click="handleClick(scope.row)" type="text" size="small">查看</el-button>
          <el-button type="text" size="small">编辑</el-button>
        </template>
      </el-table-column>
    </el-table>-->
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="listQuery.pageNum"
      :page-size="listQuery.pageRow"
      :total="totalCount"
      :page-sizes="[10, 20, 50, 100]"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
<!--    <el-dialog :title="textMap[dialogStatus]" :visible.sync="dialogFormVisible">
      <el-form class="small-space" :model="tempArticle" label-position="left" label-width="60px"
               style='width: 300px; margin-left:50px;'>
        <el-form-item label="小区基础信息">
          <el-input type="text" v-model="tempArticle.content">
          </el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button v-if="dialogStatus=='create'" type="success" @click="createArticle">创 建</el-button>
        <el-button type="primary" v-else @click="updateArticle">修 改</el-button>
      </div>
    </el-dialog>-->
  </div>
</template>
<script>
  export default {
    data() {
      return {
        totalCount: 0, //分页组件--数据总条数
        list: [],//表格的数据
        listLoading: false,//数据加载等待动画
        listQuery: {
          pageNum: 1,//页码
          pageRow: 50,//每页条数
          name: ''
        },
        dialogStatus: 'create',
        dialogFormVisible: false,
        dialogTableVisible: false,
        textMap: {
          update: '编辑',
          create: '创建公共信息'
        },
        tempArticle: {
          id: "",
          content: ""
        }
      }
    },
    created() {
      this.getList();
    },
    methods: {
      handleClick(row) {
        console.log(row);
      },
        getList() {
          //查询列表
          if (!this.hasPerm('user:list')) {
            return
          }
          this.listLoading = true;
          this.api({
            url: "/air/listair",
            method: "get",
            params: this.listQuery
          }).then(data => {
            this.listLoading = false;
            this.list = data.list;

          })
        },
        handleSizeChange(val) {
          //改变每页数量
          this.listQuery.pageRow = val
          this.handleFilter();
        },
        handleCurrentChange(val) {
          //改变页码
          this.listQuery.pageNum = val
          this.getList();
        },
        getIndex($index) {
          //表格序号
          return (this.listQuery.pageNum - 1) * this.listQuery.pageRow + $index + 1
        },
        showCreate() {
          //显示新增对话框
          this.tempArticle.content = "";
          this.dialogStatus = "create"
          this.dialogFormVisible = true
        },
        showUpdate($index) {
          //显示修改对话框
          this.tempArticle.id = this.list[$index].id;
          this.tempArticle.content = this.list[$index].content;
          this.dialogStatus = "update"
          this.dialogFormVisible = true
        }
      }

  }
</script>
