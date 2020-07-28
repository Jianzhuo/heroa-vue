<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible"
    :before-close="handleClose"
  >
    <el-form
      :model="dataForm"
      :rules="dataRule"
      ref="dataForm"
      @keyup.enter.native="dataFormSubmit()"
      label-width="80px"
    >
      <el-form-item label="工作开始" prop="workStart">
        <el-time-select
          placeholder="開始时间"
          v-model="dataForm.workStart"
          :picker-options="{
      start: '06:00',
      step: '00:30',
      end: '21:00',
      maxTime: dataForm.workEnd
    }"
        ></el-time-select>
      </el-form-item>
      <el-form-item label="工作结束" prop="workEnd">
        <el-time-select
          placeholder="结束时间"
          v-model="dataForm.workEnd"
          :picker-options="{
      start: '06:00',
      step: '00:30',
      end: '21:00',
      minTime: dataForm.workStart
    }"
        ></el-time-select>
      </el-form-item>
      <el-form-item label="工作时间" prop="durationTime">{{dataForm.durationTime = TimeDifference()}}</el-form-item>
      <!-- other worker -->
      <el-form-item label="其他人员" prop="other">
        <el-select v-model="dataForm.otherWorker" filterable multiple placeholder="请选择">
          <el-option
            v-for="item in other"
            :key="item.username"
            :label="item.username"
            :value="item.username"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="工作地点" prop="workLocation">
        <el-select v-model="dataForm.workLocation" filterable placeholder="请选择">
          <el-option
            v-for="item in jobLocation"
            :key="item.locationName"
            :label="item.locationName"
            :value="item.locationName"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="工作内容" prop="workContent">
        <el-select v-model="dataForm.workContent" filterable placeholder="请选择">
          <el-option
            v-for="item in jobContent"
            :key="item.contentName"
            :label="item.contentName"
            :value="item.contentName"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="工作细节" prop="workDetail">
        <el-input type="textarea" :rows="2" v-model="dataForm.workDetail" placeholder></el-input>
      </el-form-item>
      <el-form-item label="工作进度" prop="workProgress">
        <el-slider v-model="dataForm.workProgress" show-input :step="5" show-stops></el-slider>
      </el-form-item>
      <el-form-item label="工作备注" prop="workNote">
        <el-input type="textarea" :rows="2" v-model="dataForm.workNote" placeholder></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false; dataForm.otherWorker=[];">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
export default {
  data() {
    return {
      jobLocation: [],
      jobContent: [],
      other: [],
      visible: false,
      dataForm: {
        reportId: 0,
        workId: 0,
        workStart: "",
        workEnd: "",
        durationTime: "",
        workLocation: "",
        workContent: "",
        workDetail: "",
        workProgress: 0,
        workNote: "",
        otherWorker: []
      },
      dataRule: {
        workStart: [
          { required: true, message: "工作开始时间不能为空", trigger: "blur" }
        ],
        workEnd: [
          { required: true, message: "工作结束时间不能为空", trigger: "blur" }
        ],
        durationTime: [
          { required: true, message: "不能为空", trigger: "blur" }
        ],
        workLocation: [
          { required: true, message: "不能为空", trigger: "blur" }
        ],
        workContent: [{ required: true, message: "不能为空", trigger: "blur" }],
        workDetail: [{ required: true, message: "不能为空", trigger: "blur" }],
        workProgress: [
          { required: true, message: "不能为空", trigger: "blur" }
        ],
        workNote: [{ required: true, message: "不能为空", trigger: "blur" }]
      }
    };
  },
  methods: {
    init(report_id, work_id) {
      this.dataForm.workId = work_id || 0;
      this.dataForm.reportId = report_id || 0;
      this.visible = true;
      this.$nextTick(() => {
        this.$refs["dataForm"].resetFields();
        if (this.dataForm.workId) {
          this.$http({
            url: this.$http.adornUrl(
              `/dreport/work/info/${this.dataForm.workId}`
            ),
            method: "get",
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.workStart = data.work.workStart.substr(0, 5);
              this.dataForm.workEnd = data.work.workEnd.substr(0, 5);
              this.dataForm.durationTime = data.work.durationTime;
              this.dataForm.workLocation = data.work.workLocation;
              this.dataForm.workContent = data.work.workContent;
              this.dataForm.workDetail = data.work.workDetail;
              this.dataForm.workProgress = data.work.workProgress;
              this.dataForm.workNote = data.work.workNote;
              this.dataForm.otherWorker = data.work.workMates;
            }
          });
        }
      });
    },
    // 表单提交
    dataFormSubmit() {
      this.$refs["dataForm"].validate(valid => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(
              `/dreport/work/${!this.dataForm.workId ? "save" : "update"}`
            ),
            method: "post",
            data: this.$http.adornData({
              reportId: this.dataForm.reportId || undefined,
              workId: this.dataForm.workId || undefined,
              workStart: this.dataForm.workStart + ":00",
              workEnd: this.dataForm.workEnd + ":00",
              durationTime: this.dataForm.durationTime,
              workLocation: this.dataForm.workLocation,
              workContent: this.dataForm.workContent,
              workDetail: this.dataForm.workDetail,
              workProgress: this.dataForm.workProgress,
              workNote: this.dataForm.workNote,
              workMates: this.dataForm.otherWorker
            })
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: "操作成功",
                type: "success",
                duration: 1500,
                onClose: () => {
                  this.visible = false;
                  this.$emit("refreshDataList");
                  this.dataForm.otherWorker = [];
                }
              });
            } else {
              this.$message.error(data.msg);
            }
          });
        }
      });
    },
    TimeDifference: function() {
      if (this.dataForm.workStart != "" && this.dataForm.workEnd != "") {
        var _startTime = this.dataForm.workStart.split(":");
        var _endTime = this.dataForm.workEnd.split(":");
        var startDate = new Date(0, 0, 0, _startTime[0], _startTime[1], 0);
        var endDate = new Date(0, 0, 0, _endTime[0], _endTime[1], 0);
        var diff = endDate.getTime() - startDate.getTime();
        var hours = Math.floor(diff / 1000 / 60 / 60);
        diff -= hours * 1000 * 60 * 60;
        var minutes = Math.floor(diff / 1000 / 60);
        if (hours != 0) {
          hours = hours + "小时";
        } else {
          hours = "";
        }
        if (minutes != 0) {
          minutes = minutes + "分钟";
        } else {
          minutes = "";
        }
        return hours + minutes;
      }
      return "";
    },
    //从服务器请求所有工作地点的数据
    getLocations() {
      this.$http({
        url: this.$http.adornUrl("/dreport/worklocation/list"),
        method: "get"
      }).then(({ data }) => {
        this.jobLocation = data.page.list;
      });
    },
    //从服务器请求所有工作内容的数据
    getContent() {
      this.$http({
        url: this.$http.adornUrl("/dreport/workcontent/list"),
        method: "get"
      }).then(({ data }) => {
        this.jobContent = data.page.list;
      });
    },
    //从服务器请求所有用户的数据
    getUsers() {
      this.$http({
        url: this.$http.adornUrl("/app/user/list/constructor"),
        method: "get"
      }).then(({ data }) => {
        this.other = data.data;
      });
    },
    handleClose(done) {
      this.dataForm.otherWorker=[];
      done();
    }
  },
  created() {
    //当该组件创建完成后向服务器发送请求获得数据
    this.getLocations();
    this.getContent();
    this.getUsers();
  }
};
</script>
