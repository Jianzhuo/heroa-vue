<template>
  <div v-loading.lock="loading" element-loading-text="拼命加载中">
    <h4>今日日报</h4>
    <div v-if="check == 0"></div>
    <div v-if="check == 1">
      <p style="color:#A67A2B">亲爱的{{userName}}，今日日报已提交，今天您辛苦了。</p>
      <p>
        <i class="el-alert__icon el-icon-warning"></i> 如有补充修改，请按公司流程找Ronnie索取纸质报表完成补充申报！
      </p>
    </div>

    <div v-if="check == 2">
      <p style="color:#A67A2B">
        <i class="el-alert__icon el-icon-warning"></i> 如果当天没有完成报表，请按公司流程找Ronnie索取纸质报表完成补充申报!
      </p>
      <p>姓名： {{userName}}</p>
      <p>汇报日期： {{reportDate}}</p>
      <!-- start the form -->
      <el-form ref="dataForm" :model="creport">
        <el-card
          :key="index"
          v-for="(inputData, index) in creport.inputData"
          style="margin-bottom: 1em"
        >
          <div slot="header" class="clearfix">
            <span>工作{{index+1}}</span>
          </div>
          <!-- the end and start work time selector, the durction time will be calculated by js -->
          <el-form-item
            label="工作开始时间"
            :prop="'inputData.' + index + '.startTime'"
            :rules="[{ required: true, message: '工作开始时间不能为空', trigger: 'blur' }]"
          >
            <el-time-select
              :editable="false"
              :clearable="false"
              placeholder="开始时间"
              v-model="inputData.startTime"
              :picker-options="{
                start: '06:00',
                step: '00:30',
                end: '21:00',
                maxTime: inputData.endTime
              }"
            ></el-time-select>
          </el-form-item>
          <el-form-item
            label="工作结束时间"
            :prop="'inputData.' + index + '.endTime'"
            :rules="[{ required: true, message: '工作结束时间不能为空', trigger: 'blur' }]"
          >
            <el-time-select
              :editable="false"
              :clearable="false"
              placeholder="结束时间"
              v-model="inputData.endTime"
              :picker-options="{
                start: '06:00',
                step: '00:30',
                end: '21:00',
                minTime: inputData.startTime
              }"
            ></el-time-select>
          </el-form-item>

          <p class="report-time-duration">
            <span
              v-if="(inputData.workDurcation = TimeDifference(index))!==''"
            >工作时数：{{inputData.workDurcation}}</span>
          </p>

          <!-- work location -->
          <el-form-item
            label="工作地点"
            :prop="'inputData.' + index + '.workLocation'"
            :rules="[{ required: true, message: '工作地点不能为空', trigger: 'change' }]"
          >
            <el-select v-model="inputData.workLocation" placeholder="请选择">
              <el-option
                v-for="item in jobLocation"
                :key="item.locationName"
                :label="item.locationName"
                :value="item.locationName"
              ></el-option>
            </el-select>
          </el-form-item>

          <!-- work content -->
          <el-form-item
            label="工作内容"
            :prop="'inputData.' + index + '.workContent'"
            :rules="[{ required: true, message: '工作内容不能为空', trigger: 'change' }]"
          >
            <el-select v-model="inputData.workContent" placeholder="请选择">
              <el-option
                v-for="item in jobContent"
                :key="item.contentName"
                :label="item.contentName"
                :value="item.contentName"
              ></el-option>
            </el-select>
          </el-form-item>

          <!-- work detail -->
          <el-form-item
            label="工作内容细节"
            :prop="'inputData.' + index + '.workDetail'"
            :rules="[{ required: true, message: '工作内容细节不能为空', trigger: 'blur' }]"
          >
            <el-input type="textarea" :rows="2" placeholder="请输入内容" v-model="inputData.workDetail"></el-input>
          </el-form-item>

          <!-- work progress in % -->
          <el-form-item label="工作完成进度">
            <el-progress :percentage="inputData.workProgress" color="#8a1913"></el-progress>
            <div style="margin-top:10px">
              <el-button-group>
                <b-form-spinbutton min="5" max="100" step="5" v-model="inputData.workProgress"></b-form-spinbutton>
                <!-- <el-button icon="el-icon-minus" @click="decrease(index)"></el-button>
                <el-button icon="el-icon-plus" @click="increase(index)"></el-button>-->
              </el-button-group>
            </div>
          </el-form-item>

          <!-- other worker -->
          <el-form-item label="其他人员" prop="other">
            <el-select v-model="inputData.otherWorker" multiple placeholder="请选择">
              <el-option
                v-for="item in other"
                :key="item.username"
                :label="item.username"
                :value="item.username"
              ></el-option>
            </el-select>
          </el-form-item>

          <!-- work note -->
          <el-form-item label="备注内容" prop="note">
            <el-input type="textarea" :rows="2" placeholder="请输入内容" v-model="inputData.workNote"></el-input>
          </el-form-item>
          <!-- 添加工作 -->
          <el-button v-if="index+1 == creport.inputData.length" @click="addWork">添加工作</el-button>
          <!-- 删除工作 -->
          <el-button v-if="index>0" @click.prevent="removeWork(index)">删除</el-button>
        </el-card>
        <div style="height: 1em;"></div>

        <!-- other notes -->
        <p>其他备注</p>
        <el-input type="textarea" :rows="2" placeholder="请输入内容" v-model="creport.otherNote"></el-input>
        <br />
        <br />
        <!-- Self-evaluation -->
        <p>本日工作自我评价</p>
        <el-form-item>
          <el-rate
            v-model="creport.rate"
            :colors="['#99A9BF', '#F7BA2A', '#FF9900']"
            show-text
            :texts="['不在状态', '状态欠佳', '正常发挥','干的不错','今天牛逼啦']"
          ></el-rate>
        </el-form-item>
        <hr style="margin-bottom:20px;" />
        <!-- submit button -->
        <el-button type="primary" @click="onSubmit">提交日报</el-button>
      </el-form>
    </div>
  </div>
</template>

<script>
export default {
  inject: ["refresh"],
  name: "ReportForm",
  data() {
    return {
      jobLocation: [],
      jobContent: [],
      other: [],
      check: 0,
      loading: true,

      //收集用户填写的数据准备提交给服务器
      creport: {
        reporter: this.$store.state.user.name,
        otherNote: "",
        rate: null,
        inputData: [
          {
            startTime: "",
            endTime: "",
            workDurcation: "",
            workLocation: "",
            workContent: "",
            workDetail: "",
            workProgress: 20,
            otherWorker: [],
            workNote: ""
          }
        ]
      },
      date: new Date()
    };
  },
  computed: {
    reportDate: {
      get() {
        return (
          new Date().getFullYear() +
          "年" +
          (new Date().getMonth() + 1) +
          "月" +
          new Date().getDate() +
          "日"
        );
      }
    },
    userName: {
      get() {
        return this.$store.state.user.name;
      }
    },
    userId: {
      get() {
        return this.$store.state.user.id;
      }
    }
  },
  methods: {
    addWork() {
      this.creport.inputData.push({
        startTime: "",
        endTime: "",
        workLocation: "",
        workProgress: 20
      });
    },
    removeWork(index) {
      if (index !== 0) {
        this.creport.inputData.splice(index, 1);
      }
    },
    TimeDifference: function(index) {
      if (
        this.creport.inputData[index].startTime != "" &&
        this.creport.inputData[index].endTime != ""
      ) {
        var _startTime = this.creport.inputData[index].startTime.split(":");
        var _endTime = this.creport.inputData[index].endTime.split(":");
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
    // increase(index) {
    //   this.creport.inputData[index].workProgress += 5;
    //   if (this.creport.inputData[index].workProgress > 100) {
    //     this.creport.inputData[index].workProgress = 100;
    //   }
    // },
    // decrease(index) {
    //   this.creport.inputData[index].workProgress -= 5;
    //   if (this.creport.inputData[index].workProgress < 0) {
    //     this.creport.inputData[index].workProgress = 0;
    //   }
    // },

    //从服务器请求页面构建需要的数据
    //从服务器请求所有用户的数据
    getUsers() {
      this.$http({
        url: this.$http.adornUrl("/app/user/list/constructor"),
        method: "get",
        params: this.$http.adornParams({
          limit: 500
        })
      }).then(({ data }) => {
        this.other = data.data;
      });
    },
    //从服务器请求所有工作地点的数据
    getLocations() {
      this.$http({
        url: this.$http.adornUrl("/dreport/worklocation/list"),
        method: "get",
        params: this.$http.adornParams({
          limit: 500
        })
      }).then(({ data }) => {
        this.jobLocation = data.page.list;
      });
    },
    //从服务器请求所有工作内容的数据
    getContent() {
      this.$http({
        url: this.$http.adornUrl("/dreport/workcontent/list"),
        method: "get",
        params: this.$http.adornParams({
          limit: 500
        })
      }).then(({ data }) => {
        this.jobContent = data.page.list;
      });
    },
    //上传用户填写的日报数据给服务器
    onSubmit() {
      this.$refs["dataForm"].validate(valid => {
        if (valid) {
          this.loading = true;
          this.$http({
            url: this.$http.adornUrl("/dreport/api/save"),
            method: "post",
            data: this.$http.adornData(this.creport, false)
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: "日报提交成功",
                type: "success",
                duration: 1500,
                onClose: () => {
                  this.refresh();
                }
              });
            } else {
              this.loading = false;
              this.$message.error("提交失败，请稍后重试！");
            }
          });
        }else{
          this.$message.error("日报填写不完整，请检查后重新提交！");
        }
      });
    },
    //向服务器发送请求检查当前用户当天是否提交过日报
    checkSubmit() {
      // this.$http({
      //   url: this.$http.adornUrl("/dreport/api/check/" + this.userName),
      //   method: "get"
      // }).then(({ data }) => {
      //   if (data.data > 0) {
      //     this.check = 1;
      //   } else {
          this.getUsers();
          this.getLocations();
          this.getContent();
          this.check = 2;
        // }
        this.loading = false;
      // });
    }
  },
  //生命周期 - 创建完成 可访问当前this实例
  created() {
    //当该组件创建完成后向服务器发送请求获得数据
    this.checkSubmit();
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.report-time-duration {
  margin: 0 0 22px;
  font-weight: 600;
  color: #a67a2b;
}
.time-select-item.selected:not(.disabled) {
  color: #8a1913 !important;
}
hr {
  border-color: rgba(255, 255, 255, 0.38);
}
.el-select {
  width: 100%;
}
</style>
