
<template>


  <div>
    <div class="card" style="padding: 15px">
      <span style="margin-bottom: 30px; font-size: 20px; font-weight: bold;">欢迎您，{{ user?.name }}</span>
      <div class="el-timeline-item__timestamp is-bottom">
        {{ nowTime }}
      </div>
    </div>

    <div class="box">
      <div class="item">
        总用户人数
      </div>
      <div class="item">
        志愿者人数
      </div>
      <div class="item">
        需求者人数
      </div>
      <div class="item">
        任务总数量
      </div>
      <div class="item">
        交易总金额
      </div>
    </div>

    <div style="display: flex; margin: 10px 0px; justify-content: space-between;">
      <div style="width: 45%;" class="card">
        <div style="margin-bottom: 30px; font-size: 20px; font-weight: bold; text-align: center;">公告列表</div>
        <div>
          <el-timeline reverse slot="reference">
            <el-timeline-item v-for="item in notices" :key="item.id" :timestamp="item.time">
              <el-popover placement="right" width="200" trigger="hover" :content="item.content">
                <span slot="reference">{{ item.title }}</span>
              </el-popover>
            </el-timeline-item>
          </el-timeline>
        </div>
      </div style="width: 45%;" class="card">
      <div id="line" style="width: 650px; height: 500px">
      </div>
    </div>
  </div>

</template>
<script>

export default {
  name: 'Home',
  data() {
    return {
      user: JSON.parse(localStorage.getItem('xm-user') || '{}'),
      notices: [],
      nowTime: ""
    }
  },
  created() {
    this.$request.get('/notice/selectAll').then(res => {
      this.notices = res.data || []
    })
  },
  mounted() {
    this.myEcharts();
    this.getNowTime();
    clearInterval(myTimeDisplay);//销毁之前定时器
    var myTimeDisplay = setInterval(() => {
      this.getNowTime(); //每秒更新一次时间
    }, 1000);
  },
  methods: {
    myEcharts() {
      var line = this.$echarts.init(document.getElementById("line"));
      //配置图表
      var option = {
        title: {
          text: '志愿服务处理情况',
          subtext: '麦园小区',
          left: 'center'
        },
        tooltip: {
          trigger: 'item'
        },
        legend: {
          orient: 'vertical',
          left: 'left'
        },
        series: [
          {
            name: 'Access From',
            type: 'pie',
            radius: '50%',
            data: [
              { value: 1048, name: '待审核任务' },
              { value: 735, name: '待接受任务' },
              { value: 580, name: '已接受任务' },
              { value: 484, name: '待评价任务' },
              { value: 300, name: '已完成任务' },
              { value: 300, name: '已取消任务' },
            ],
            emphasis: {
              itemStyle: {
                shadowBlur: 10,
                shadowOffsetX: 0,
                shadowColor: 'rgba(0, 0, 0, 0.5)'
              }
            }
          }
        ]
      };
      line.setOption(option);
    },
    getNowTime() {
      var date = new Date();
      var time = date.getFullYear() + ' 年 ' + (date.getMonth() + 1) + ' 月 ' + date.getDate() + ' 日 ' + date.getHours() + ':' + this.addZero(date.getMinutes()) + ':' + this.addZero(date.getSeconds());
      this.nowTime = time;
    },
    addZero(s) {
            return s < 10 ? ('0' + s) : s;
        },

  },
}

</script>

<style>
.box {
  display: flex;
  flex-flow: row wrap;
  justify-content: space-between;
  width: 100%;
  height: 100%;
}

.item {
  background-color: blueviolet;
  text-align: center;
  width: 15%;
  height: 100px;
  padding: 15px;
  margin: 10px 0px;
}
</style>
