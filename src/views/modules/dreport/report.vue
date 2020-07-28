<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="参数名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button
          type="danger"
          @click="deleteHandle()"
          :disabled="dataListSelections.length <= 0"
        >批量删除</el-button>
        <el-button type="primary" @click="reportExport()">导出</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;"
    >
      <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
      <el-table-column prop="reportId" header-align="center" align="center" label="日报id"></el-table-column>
      <el-table-column
        sortable
        header-align="center"
        align="center"
        label="日报提交时间"
        prop="reportTime"
      >
        <template slot-scope="prop">
          <span>{{prop.row.reportTime.substr(0,10)}}</span>
        </template>
      </el-table-column>
      <el-table-column prop="reportUserName" header-align="center" align="center" label="日报提交者"></el-table-column>
      <el-table-column type="expand" label="工作" width="90">
        <template slot-scope="props">
          <el-table :data="props.row.works" border v-loading="dataListLoading" style="width: 100%;">
            <el-table-column prop="workId" header-align="center" align="center" label="工作id"></el-table-column>
            <el-table-column header-align="center" align="center" label="工作开始">
              <template slot-scope="prop">
                <span>{{prop.row.workStart.substr(0,5)}}</span>
              </template>
            </el-table-column>
            <el-table-column prop="workEnd" header-align="center" align="center" label="工作结束">
              <template slot-scope="prop">
                <span>{{prop.row.workEnd.substr(0,5)}}</span>
              </template>
            </el-table-column>
            <el-table-column prop="workMate" header-align="center" align="center" label="同组人员"></el-table-column>
            <el-table-column prop="durationTime" header-align="center" align="center" label="工作时间"></el-table-column>
            <el-table-column prop="workLocation" header-align="center" align="center" label="工作地点"></el-table-column>
            <el-table-column prop="workContent" header-align="center" align="center" label="工作内容"></el-table-column>
            <el-table-column prop="workDetail" header-align="center" align="center" label="工作细节"></el-table-column>
            <el-table-column prop="workProgress" header-align="center" align="center" label="工作进度"></el-table-column>
            <el-table-column prop="workNote" header-align="center" align="center" label="工作备注"></el-table-column>
            <el-table-column
              fixed="right"
              header-align="center"
              align="center"
              width="100"
              label="操作"
            >
              <template slot-scope="scope">
                <el-button
                  type="text"
                  size="small"
                  @click="workAddOrUpdateHandle(props.row.reportId, scope.row.workId)"
                >修改</el-button>
                <el-button type="text" size="small" @click="workDeleteHandle(scope.row.workId)">删除</el-button>
              </template>
            </el-table-column>
          </el-table>
          <el-form :inline="true" style="float:right">
            <el-form-item>
              <el-button type="primary" @click="workAddOrUpdateHandle(props.row.reportId)">新增</el-button>
            </el-form-item>
          </el-form>
        </template>
      </el-table-column>
      <el-table-column prop="reportNote" header-align="center" align="center" label="日报备注"></el-table-column>
      <el-table-column prop="reportRate" header-align="center" align="center" label="自我评价"></el-table-column>
      <el-table-column fixed="right" header-align="center" align="center" width="150" label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.reportId)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.reportId)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper"
    ></el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
    <WorkAddOrUpdate
      v-if="workAddOrUpdateVisible"
      ref="workAddOrUpdate"
      @refreshDataList="getDataList"
    ></WorkAddOrUpdate>
  </div>
</template>

<script>
import AddOrUpdate from "./report-add-or-update";
import WorkAddOrUpdate from "./work-add-or-update";
export default {
  data() {
    return {
      dataForm: {
        key: ""
      },
      dataList: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      dataListSelections: [],
      addOrUpdateVisible: false,
      workAddOrUpdateVisible: false
    };
  },
  components: {
    AddOrUpdate,
    WorkAddOrUpdate
  },
  activated() {
    this.getDataList();
  },
  methods: {
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/dreport/report/list"),
        method: "get",
        params: this.$http.adornParams({
          page: this.pageIndex,
          limit: this.pageSize,
          key: this.dataForm.key
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list;
          this.totalPage = data.page.totalCount;
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    // 每页数
    sizeChangeHandle(val) {
      this.pageSize = val;
      this.pageIndex = 1;
      this.getDataList();
    },
    // 当前页
    currentChangeHandle(val) {
      this.pageIndex = val;
      this.getDataList();
    },
    // 多选
    selectionChangeHandle(val) {
      this.dataListSelections = val;
    },
    // report 新增 / 修改
    addOrUpdateHandle(id) {
      this.addOrUpdateVisible = true;
      this.$nextTick(() => {
        this.$refs.addOrUpdate.init(id);
      });
    },
    // work 新增 / 修改
    workAddOrUpdateHandle(report_id, work_id) {
      this.workAddOrUpdateVisible = true;
      this.$nextTick(() => {
        this.$refs.workAddOrUpdate.init(report_id, work_id);
      });
    },
    // 删除
    deleteHandle(id) {
      var ids = id
        ? [id]
        : this.dataListSelections.map(item => {
            return item.reportId;
          });
      this.$confirm(
        `确定对[report_id=${ids.join(",")}]进行[${
          id ? "删除" : "批量删除"
        }]操作?`,
        "提示",
        {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning"
        }
      ).then(() => {
        this.$http({
          url: this.$http.adornUrl("/dreport/report/delete"),
          method: "post",
          data: this.$http.adornData(ids, false)
        }).then(({ data }) => {
          if (data && data.code === 0) {
            this.$message({
              message: "操作成功",
              type: "success",
              duration: 1500,
              onClose: () => {
                this.getDataList();
              }
            });
          } else {
            this.$message.error(data.msg);
          }
        });
      });
    },
    workDeleteHandle(id) {
      var ids = [id];
      this.$confirm(
        `确定对[work_id=${ids.join(",")}]进行[${
          id ? "删除" : "批量删除"
        }]操作?`,
        "提示",
        {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning"
        }
      ).then(() => {
        this.$http({
          url: this.$http.adornUrl("/dreport/work/delete"),
          method: "post",
          data: this.$http.adornData(ids, false)
        }).then(({ data }) => {
          if (data && data.code === 0) {
            this.$message({
              message: "操作成功",
              type: "success",
              duration: 1500,
              onClose: () => {
                this.getDataList();
              }
            });
          } else {
            this.$message.error(data.msg);
          }
        });
      });
    },
    // 导出
    reportExport(id) {
      var ids = id
        ? [id]
        : this.dataListSelections.map(item => {
            return item.reportId;
          });
      this.$http({
        url: this.$http.adornUrl("/dreport/api/doExport"),
        method: "post",
        data: this.$http.adornData(ids, false),
        responseType: "arraybuffer"
      })
        .then(function(response) {
          var date = new Date;
          if ((response.ok = true)) {
            //获取自定义文件名
            var fileName = "ConstructorReport-"+date.getFullYear()+"-"+date.getMonth()+"-"+date.getDate()+"-"+date.getHours()+":"+date.getMinutes()+":"+date.getSeconds()+".xls";
            var objectUrl = URL.createObjectURL(new Blob([response.data]));
            var link = document.createElement("a");
            console.log(link);
            link.download = decodeURIComponent(fileName);
            link.href = objectUrl;
            link.click();
          } 
        })
        .catch(function(error) {
          console.log(error);
          
        });
    }
  }
};
</script>

<style scoped>
</style>
