<template>
  <div id="datePicker">
    <!-- 输入时间 -->
    <div class="input">
      <!-- 因为props 中的值不能更改 所以不能使用 v-model -->
      <!--  -->
      <input type="text" @blur="findDate(date)" :placeholder="formatDate" v-model="date" />
    </div>
    <!-- 日期面板 -->
    <div class="datePanel">
      <div class="panel-nav" ref="nav">
        <span class="lastYear iconfont icon-jiantouzuo" @click="preyear"></span>
        <span class="lastYear iconfont icon-shuangjiantouzuo" @click="premonth"></span>
        <span class="title">{{time}}</span>
        <span class="lastYear iconfont icon-shuangjiantouyou" @click="nextmonth"></span>
        <span class="lastYear iconfont icon-jiantouyou" @click="nextyear"></span>
      </div>
      <div class="panel-content">
        <!-- 循环一个6*7 表格 -->
        <!-- i 和 j 是从1开始 -->
        <div class="week">
          <span v-for="item in week" :key="item">{{item}}</span>
        </div>
        <div class="date" v-for="i in 6" :key="i">
          <!-- 判断是不是当月 不是字体变灰 -->
          <span
            v-for="j in 7"
            :key="j"
            @click="chooseDate(visibeDays[(i-1)*7+(j-1)])"
            :class="[{notCurrentMonth:!isCurrentMonth(visibeDays[(i-1)*7+(j-1)])},{CurrentToday:isCurrentToday(visibeDays[(i-1)*7+(j-1)])},{select:isSelect(visibeDays[(i-1)*7+(j-1)])}]"
          >{{visibeDays[(i-1)*7+(j-1)]|dateFormat}}</span>
        </div>
      </div>
      <div class="panel-footer" @click="backToDay(value.default)">回到今天</div>
    </div>
  </div>
</template>

<script>
import moment from 'moment'
import 'moment/locale/zh-cn'
export default {
  data () {
    return {
      date: '',
      time: moment(this.value).format('l'),
      week: ['日', '一', '二', '三', '四', '五', '六'],
    }
  },
  props: {
    value: {
      default: () => new Date //默认值 当前时间  {} [] 返回的类型必须是函数类型
    }
  },
  methods: {
    // 是不是当月
    isCurrentMonth (data) {
      //拿到当前data月份
      let month = moment(data).format('MM')
      let yy = moment(data).format('YYYY')
      //拿到当前时间的月份
      let nowMonth = moment(this.time).format('MM')
      let toyy = moment(this.time).format('YYYY')
      return month === nowMonth && yy === toyy
    },
    //判断是不是当天
    isCurrentToday (day) {
      let yy = moment(day).format('YYYY')
      let mm = moment(day).format('MM')
      let dd = moment(day).format('DD')
      //拿到当前时间的日期
      let toyy = moment(new Date()).format('YYYY')
      let tomonth = moment(new Date()).format('MM')
      let today = moment(new Date()).format('DD')
      // 年月日都相等
      return dd === today && mm === tomonth && yy === toyy
    },
    //判断是否选中
    isSelect (date) {
      let yy = moment(this.time).format('YYYY')
      let mm = moment(this.time).format('MM')
      let dd = moment(this.time).format('DD')
      let isyy = moment(date).format('YYYY')
      let ismm = moment(date).format('MM')
      let isdd = moment(date).format('DD')
      return yy === isyy && mm === ismm && dd === isdd
    },
    //显示日期
    chooseDate (date) {
      this.$emit('input', date)
      this.time = moment(date).format('l')
    },
    //上一年
    preyear () {
      this.time = moment(this.time).subtract(1, 'year').format('l')
    },
    //上一月
    premonth () {
      this.time = moment(this.time).subtract(1, 'month').format('l')
    },
    //下一月
    nextmonth () {
      this.time = moment(this.time).add(1, 'month').format('l')
    },
    //下一年
    nextyear () {
      this.time = moment(this.time).add(1, 'year').format('l')
    },
    //回到今天
    backToDay (value) {
      this.time = moment(value).format('l')
    },
    //查找日期
    findDate (date) {
      if (!date.trim()) {
        this.time = moment(this.value.default).format('l')
        return
      }
      this.time = moment(date).format('l')
      console.log(date.trim());

    }
  },
  computed: {
    // 日期数组
    visibeDays () {
      //拿到this.time的年份
      let year = moment(this.time).format('YYYY')
      //拿到this.time的月份
      let month = moment(this.time).format('MM')
      //拿到当月第一天的时间戳
      let currentFirstDay = moment(year + '-' + month + '-' + 1).format('x')
      // 获取本月第一天是周几
      let week = moment(+currentFirstDay).format('d')
      //拿到格子第一格的时间戳 本月第一天是周几就把天数往前移几天  例如当前周3 就用本月第一天减3
      let startDay = currentFirstDay - week * 60 * 60 * 24 * 1000
      //循环42天 42个格子
      let arr = [];
      for (let i = 0; i < 42; i++) {
        //从格子第一天开始 每循环一次向后加一天
        arr.push(moment(startDay + i * 60 * 60 * 24 * 1000).format('YYYY-MM-DD'))
      }
      return arr
    },
    //显示 父组件传递日期时间的计算属性
    formatDate () {
      return moment(this.value).format('YYYY-MM-DD')
    }
  },
  filters: {
    // 定义多少号的过滤器
    dateFormat (time) {
      return moment(time).format('DD')
    }
  }
}
</script>

<style lang="less" scoped>
#datePicker {
  // 不是本月
  .notCurrentMonth {
    color: #ccc !important;
  }
  //当天
  .CurrentToday {
    background-color: #87ceeb;
    border-radius: 50%;
    font-weight: 700;
  }
  // //选中
  .select {
    border: 1px solid #87ceeb;
    border-radius: 50%;
  }
  .input {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: 3.125rem;
    input {
      width: 60%;
      height: 90%;
      padding: 0 1.25rem;
      border-radius: 50px;
    }
  }
  .datePanel {
    width: 18.75rem;
    margin: 2.125rem auto;
    box-shadow: 5px 5px 10px gray;
    .panel-nav {
      display: flex;
      font-size: 1.5rem;
      align-items: center;
      color: #fff;
      text-align: center;
      padding: 1.25rem;
      .iconfont {
        flex: 1;
        font-size: 1.875rem;
        cursor: pointer;
      }
      .title {
        flex: 6;
      }
    }
    .panel-content {
      background-color: #fff;
      padding: 1.25rem;
      text-align: center;
      span {
        padding: 0.625rem;
      }
      .week {
        color: #87ceeb;
        display: flex;
        span {
          flex: 1;
        }
      }
      .date {
        padding: 0.625rem 0;
        display: flex;
        justify-content: space-evenly;

        span {
          display: inline-flex;
          justify-content: center;
          align-items: center;
          width: 1.875rem;
          height: 1.875rem;
          cursor: pointer;
        }
        span:hover {
          border: 1px solid #87ceeb;
          border-radius: 50%;
        }
      }
    }
    .panel-footer {
      padding: 0.625rem 0;
      text-align: center;
      cursor: pointer;
    }
  }
}
</style>