<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible"
  >
    <el-form
      :model="dataForm"
      :rules="dataRule"
      ref="dataForm"
      @keyup.enter.native="dataFormSubmit()"
      label-width="80px"
    >
      <el-form-item label="日报提交时间" label-width="auto" prop="reportTime">
        <el-date-picker
          v-model="dataForm.reportTime"
          align="right"
          type="date"
          placeholder="选择日期"
          :picker-options="pickerOptions"
        ></el-date-picker>
      </el-form-item>
      <el-form-item label="日报提交者用户id" label-width="auto" prop="reportUserName">
        <el-select v-model="dataForm.reportUserName" filterable placeholder="请选择">
          <el-option
            v-for="item in other"
            :key="item.username"
            :label="item.username"
            :value="item.username"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="日报备注" prop="reportNote">
        <el-input v-model="dataForm.reportNote" placeholder></el-input>
      </el-form-item>
      <el-form-item label="自我评价" prop="reportRate">
        <el-rate
          v-model="dataForm.reportRate"
          :colors="['#99A9BF', '#F7BA2A', '#FF9900']"
          show-text
          :texts="['不在状态', '状态欠佳', '正常发挥','干的不错','今天牛逼啦']"
        ></el-rate>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
export default {
  data() {
    return {
      other: [],
      pickerOptions: {
        disabledDate(time) {
          return time.getTime() > Date.now();
        },
        shortcuts: [
          {
            text: "今天",
            onClick(picker) {
              picker.$emit("pick", new Date());
            }
          },
          {
            text: "昨天",
            onClick(picker) {
              const date = new Date();
              date.setTime(date.getTime() - 3600 * 1000 * 24);
              picker.$emit("pick", date);
            }
          },
          {
            text: "一周前",
            onClick(picker) {
              const date = new Date();
              date.setTime(date.getTime() - 3600 * 1000 * 24 * 7);
              picker.$emit("pick", date);
            }
          }
        ]
      },
      visible: false,
      dataForm: {
        reportId: 0,
        reportTime: "",
        reportUserName: "",
        reportNote: "",
        reportRate: 0
      },
      dataRule: {
        reportTime: [
          { required: true, message: "日报提交时间不能为空", trigger: "blur" }
        ],
        reportUserName: [
          {
            required: true,
            message: "日报提交者用户id不能为空",
            trigger: "blur"
          }
        ],
        reportNote: [{ required: true, message: "不能为空", trigger: "blur" }],
        reportRate: [{ required: true, message: "不能为空", trigger: "blur" }]
      }
    };
  },
  methods: {
    init(id) {
      this.dataForm.reportId = id || 0;
      this.visible = true;
      this.$nextTick(() => {
        this.$refs["dataForm"].resetFields();
        if (this.dataForm.reportId) {
          this.$http({
            url: this.$http.adornUrl(
              `/dreport/report/info/${this.dataForm.reportId}`
            ),
            method: "get",
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.reportTime = data.report.reportTime;
              this.dataForm.reportUserName = data.report.reportUserName;
              this.dataForm.reportNote = data.report.reportNote;
              this.dataForm.reportRate = data.report.reportRate;
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
              `/dreport/report/${!this.dataForm.reportId ? "save" : "update"}`
            ),
            method: "post",
            data: this.$http.adornData({
              reportId: this.dataForm.reportId || undefined,
              reportTime: this.dataForm.reportTime,
              reportUserName: this.dataForm.reportUserName,
              reportNote: this.dataForm.reportNote,
              reportRate: this.dataForm.reportRate
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
                }
              });
            } else {
              this.$message.error(data.msg);
            }
          });
        }
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
    }
  },
  created() {
    //当该组件创建完成后向服务器发送请求获得数据
    this.getUsers();
  }
};
</script>
