<style scoped>
.ex-table .ex-table-th,
.ex-table .ex-table-td {
  text-align: center;
}

.c-container label {
  min-width: initial;
  text-align: center;
  line-height: 32px;
  color: #3f3f3f;
  padding: 0;
  float: none;
}

/*---------------------------------------------*/
/*style*/
.c-container {
  min-width: 100px;
  width: 238px;
  display: inline-block;
}

.input-contain {
  width: 100%;
  min-height: 30px;
  position: relative;
}

.c-type-input {
  width: 100%;
  /* height: 30px;
  line-height: 28px;
  padding: 0 21px 0 4px; */
  border: 1px solid #e8e8e8 !important;
  border-radius: 2px;
  box-sizing: border-box;
  word-break: keep-all;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.v-icon-date {
  width: 30px;
  height: 100%;
  position: absolute;
  top: 0;
  right: 0;
  /* background: url("./calendar.png") no-repeat center; */
  cursor: pointer;
}

/*   ------------------------------------  date input and icon  ------------------------------------   */
.c-datetime {
  width: 280px;
  padding: 0 6px;
  border: 1px solid #e8e8e8;
  background: white;
  position: absolute;
  /* top: 40px; */
  /* left: 0; */
  margin-top: 2px;
}

.c-date-year {
  line-height: 40px;
}

.c-date-year > div {
  margin: 0 auto;
  display: inline-block;
}

.c-date-year span {
  min-width: 30px;
  height: 100%;
  text-align: center;
  display: inline-block;
  cursor: pointer;
}

.c-date-year span.show-year,
.c-date-year span.show-month {
  min-width: 67px;
}

.btn-prev {
  float: left;
}

.btn-next {
  float: right;
}

/*   ------------------------------------  end   date header  ------------------------------------   */

.ex-table .ex-table-tr,
.ex-table .ex-table-tr .ex-table-th,
.ex-table .ex-table-tr .ex-table-td {
  background-color: white;
}

.dp-days {
  line-height: 30px;
  width: calc(100% / 7);
}

.dp-months {
  width: 25%;
  line-height: 47px;
  padding: 10px 0;
}

.ex-table-tr label {
  font-size: 12px;
  width: 100%;
  height: 100%;
  cursor: pointer;
  display: inline-block;
}

.ex-table-td label:hover {
  background-color: #e5e9f2;
}

.next-month label:hover {
  background-color: transparent;
}

.ex-table-tr .next-month label {
  color: #ccc;
  /*background-color: white;*/
}

.ex-table-tr > div {
  display: inline-block;
}

.ex-table-tr .dp-disabled label,
input.dp-disabled {
  /* color: #ccc;
    background-color: #f4f4f4; */
  cursor: not-allowed;
  readonly: true;
}

.ex-table-tr .dp-selected label {
  background-color: #007aff;
  color: white;
}

.c-date-year span:hover,
.ex-table-tr .dp-today label {
  color: #007aff;
}

.c-date-btn {
  width: 100%;
  border-top: 1px solid #ebebeb;
}

.c-date-btn > select {
  width: 56px;
  height: 26px;
  border: 1px solid #007aff;
  margin: 5px;
}

.c-date-btn > button {
  width: 60px;
  line-height: 26px;
  border: 0;
  color: white;
  background-color: #007aff;
  margin: 5px 5px;
  float: right;
}

.c-fixed {
  z-index: 106;
}
</style>
<template>
  <div
    class="c-container"
    :style="{ width: dtWidth }"
  >
    <div
      class="input-contain"
      ref="reference"
    >
      <input
        class="c-type-input"
        type="text"
        @blur="editorDate"
        :class="[dp_disable ? 'dp-disabled' : '']"
        :readonly="!dp_editable"
        v-model="dp_confirm_value"
        :placeholder="placeholder"
        @click="toggleDate"
      />
      <i
        class="v-icon-date"
        @click="toggleDate"
      ></i>
    </div>

    <div
      class="c-fixed c-datetime"
      ref="datetimePicker"
      v-show="dp_is_show"
    >
      <!-- -------------------- start change year month -------------------- -->
      <div class="c-date-year">
        <span
          class="btn-prev"
          @click="nextYear(-1)"
        >&lt;&lt;</span>
        <span
          class="btn-prev"
          @click="nextMonth(-1)"
          v-show="dp_show.is_days"
        >&lt;</span>
        <div>
          <span v-show="!dp_show.is_days && !dp_show.is_months"></span>
          <span
            class="show-month"
            v-show="dp_show.is_months"
          ></span>
          <span
            class="show-year"
            @click="dateClick('is_years')"
          >{{dp_option.thisYear}}&nbsp;年</span>
          <span
            class="show-month"
            @click="dateClick('is_months')"
            v-show="dp_show.is_days"
          >{{dp_month[dp_option.thisMonth].cnText}}</span>
        </div>
        <span
          class="btn-next"
          @click="nextYear(1)"
        >&gt;&gt;</span>
        <span
          class="btn-next"
          @click="nextMonth(1)"
          v-show="dp_show.is_days"
        >&gt;</span>
      </div>
      <!-- -------------------- end change year month -------------------- -->
      <!-- -------------------- start days -------------------- -->
      <div
        class="c-days"
        v-show="dp_show.is_days"
      >
        <div class="ex-table">
          <div class="ex-table-tr">
            <div
              class="ex-table-th"
              style="width: calc(100% / 7);"
              v-for="week in dp_week"
            >{{week}}</div>
          </div>
          <div
            class="ex-table-tr"
            v-if="dp_day.length>0"
            v-for="row in dp_row"
          >
            <div
              class="ex-table-td dp-days"
              :class="dp_day[(row -1) *7 + (col-1)] && dp_day[(row -1) *7 + (col-1)].status"
              v-for="col in dp_col"
            >
              <label v-if="dp_day[(row -1) *7 + (col-1)].status === 'dp-disabled'">
                {{dp_day[(row - 1) * 7 + (col - 1)] && dp_day[(row - 1) * 7 + (col - 1)].text}}
              </label>
              <label
                v-else
                @click="pickDate((row -1) *7 + (col-1))"
              >
                {{dp_day[(row - 1) * 7 + (col - 1)] && dp_day[(row - 1) * 7 + (col - 1)].text}}
              </label>
            </div>
          </div>
        </div>
        <div class="c-date-btn">
          <button @click="clearConfim">清空</button>
        </div>
        <div
          class="c-date-btn"
          v-show="dtTime"
        >
          <select
            @change="getNowTime('Hour')"
            ref="Hour"
          >
            <option
              v-for="hours in 24"
              v-if="dp_option.thisHour!==hours-1"
            >{{hours - 1}}</option>
            <option
              v-for="hours in 24"
              v-if="dp_option.thisHour===hours-1"
              selected
            >{{hours - 1}}</option>
          </select>时
          <select
            @change="getNowTime('Minute')"
            ref="Minute"
          >
            <option
              v-for="minutes in 60"
              v-if="dp_option.thisMinute!==minutes-1"
            >{{minutes - 1}}</option>
            <option
              v-for="minutes in 60"
              v-if="dp_option.thisMinute===minutes-1"
              selected
            >{{minutes - 1}}
            </option>
          </select>分
          <select
            @change="getNowTime('Second')"
            ref="Second"
          >
            <option
              v-for="seconds in 60"
              v-if="dp_option.thisSecond!==seconds-1"
            >{{seconds - 1}}</option>
            <option
              v-for="seconds in 60"
              v-if="dp_option.thisSecond===seconds-1"
              selected
            >{{seconds - 1}}
            </option>
          </select>秒
        </div>
        <div
          class="c-date-btn"
          v-show="dtTime"
        >
          <button @click="confirmDate">确定</button>
          <button @click="getNowDate">此刻</button>
        </div>
      </div>
      <!-- -------------------- end days -------------------- -->
      <!-- -------------------- start months -------------------- -->
      <div
        class="c-days"
        v-show="dp_show.is_months"
      >
        <div class="ex-table">
          <div
            class="ex-table-tr"
            v-for="row in 3"
          >
            <div
              class="ex-table-td dp-months"
              :class="dp_month[(row -1) *4 + (col-1)] && dp_month[(row -1) *4 + (col-1)].status"
              v-for="col in 4"
            >
              <label v-if="dp_month[(row -1) *4 + (col-1)].status === 'dp-disabled'">
                {{dp_month[(row - 1) * 4 + (col - 1)].cnText}}
              </label>
              <label
                v-else
                @click="pickMonth((row -1) *4 + (col-1))"
              >
                {{dp_month[(row - 1) * 4 + (col - 1)].cnText}}
              </label>
            </div>
          </div>
        </div>
      </div>
      <!-- -------------------- end months -------------------- -->
      <!-- -------------------- end years -------------------- -->
      <div
        class="c-days"
        v-show="dp_show.is_years"
      >
        <div
          class="ex-table"
          v-if="dp_year.length>1"
        >
          <div
            class="ex-table-tr"
            v-for="row in 3"
          >
            <div
              class="ex-table-td dp-months"
              :class="dp_year[(row -1) *4 + (col-1)] && dp_year[(row -1) *4 + (col-1)].status"
              v-for="col in 4"
            >
              <label
                @click="pickYear((row -1) *4 + (col-1))"
                v-if="(row -1) *4 + (col-1)<10"
              >
                {{dp_year[(row - 1) * 4 + (col - 1)].text}}
              </label>
            </div>
          </div>
        </div>
      </div>
      <!-- -------------------- start years -------------------- -->
    </div>
  </div>
</template>
<script>
const Popper = require('popper.js/dist/umd/popper.js');
/**
 **   dtWidth          输入框尺寸
 **   value          输入框内容
 **   dtMin            最低选择（当前的不可选）
 **   dtMax            最高选择（当前的不可选）
 **   dtTime           是否全时间
 **   dtEditable       文本框可输入
 **   dtReadonly       完全只读
 **   dtDisable        禁用
 **   dtFormat         日期格式化
 **   placeholder      提示
 */
export default {
  props: {
    dtWidth: { type: String, default: '220px' },
    value: { type: [String,Object], default: '' },
    dtMin: { type: String, default: '' },
    dtMax: { type: String, default: '' },
    dtTime: { type: Boolean, default: true },
    dtEditable: { type: Boolean, default: false },
    dtReadonly: { type: Boolean, default: false },
    dtDisable: { type: Boolean, default: false },
    dtFormat: { type: String, default: 'yyyy-MM-dd' },
    placeholder: { type: String, default: '' },
    placement: {
      type: String,
      default: 'bottom-start'
    },
  },
  data: function () {
    return {
      dp_col: 7,
      dp_row: 6,
      dp_is_show: false,
      dp_show: {
        is_days: true,
        is_months: false,
        is_years: false
      },
      dp_value: '',
      dp_confirm_value: '',
      dp_day: [],
      dp_week: ['日', '一', '二', '三', '四', '五', '六'],
      //                dp_month: ['一月', '二月', '三月', '四月', '五月', '六月', '七月', '八月', '九月', '十月', '十一月', '十二月'],
      dp_month: [
        { cnText: '一月', enText: 'Jan', status: '' },
        { cnText: '二月', enText: 'Feb', status: '' },
        { cnText: '三月', enText: 'Mar', status: '' },
        { cnText: '四月', enText: 'Apr', status: '' },
        { cnText: '五月', enText: 'May', status: '' },
        { cnText: '六月', enText: 'Jun', status: '' },
        { cnText: '七月', enText: 'Jul', status: '' },
        { cnText: '八月', enText: 'Aug', status: '' },
        { cnText: '九月', enText: 'Sep', status: '' },
        { cnText: '十月', enText: 'Oct', status: '' },
        { cnText: '十一月', enText: 'Nov', status: '' },
        { cnText: '十二月', enText: 'Dec', status: '' }
      ],
      dp_year: [
        { years: 2017, text: '2017', status: '' }
      ],
      dp_option: {
        thisMonth: 0,
        thisYear: 1990,
        thisHour: 0,
        thisMinute: 0,
        thisSecond: 0,
        timeChange: false
      },
      dp_now: this.getSevertime(),
      dp_editable: false,
      dp_readonly: false,
      dp_disable: false,
      popper: null,
      popperStatus: false,
    };
  },
  watch: {
    dtReadonly() {
      this.dp_readonly = this.dtReadonly;
    },
    dp_now() {
      if (this.dp_is_show === true) {
        if (this.dp_show.is_days) {
          this.updateDate();
        } else if (this.dp_show.is_months) {
          this.updateMonth();
        } else if (this.dp_show.is_years) {
          this.updateYear();
        }
      }
    },
    dp_is_show() {
      if (this.dp_is_show === true) {
        this.$refs.datetimePicker.style.marginLeft = -(268 - parseInt(this.dtWidth)) / 2 + "px";
        this.updateDate();
        this.update()
      } else {
        this.destroyPopper()
      }
    },
    value(val) {
      this.initDate(val);
    }
  },
  methods: {
    initDate(val) { //处理传进来的时间
    // console.log(this.commom.clone(),'common')
	  if(typeof val=="object"){
    // var dv = this.common.clone(val.$date);
		var dv = this.common.clone(val);
		this.dp_value = this.stringify(new Date(dv),this.dtFormat);
	  }else{
		this.dp_value = !val?'':val;
	  }
       // 提前默认赋值
      var nowServerDate = this.getSevertime();
      if (typeof val === "string" && val !== "") {
        if (val === "today") {
          this.dp_value = this.stringify(nowServerDate);
        } else if (val !== "") {
          this.dp_value = this.stringify(new Date(val));
        }
      }
      // console.log(this.dp_value,'vualue')
      this.dp_confirm_value = this.dp_value;
      this.dp_now = this.parse(this.dp_value) || nowServerDate;
      this.dp_option.thisHour = this.dp_now.getHours();
      this.dp_option.thisMinute = this.dp_now.getMinutes();
      this.dp_option.thisSecond = this.dp_now.getSeconds();
    },
    toggleDate: function (event) {
      if (!this.dp_readonly) {
        this.dp_is_show = !this.dp_is_show;
      }
    },
    closeDate: function () {
      this.dp_is_show = false;
    },
    updateDate: function () {
      var nowServerDate = this.getSevertime();
      let arr = [];
      let time = new Date(this.dp_now);
      let min = this.dtMin;
      let max = this.dtMax;
      time.setMonth(time.getMonth(), 1);       // the first day
      let detailDay = time.getDay();
      detailDay === 0 && (detailDay = 7);
      time.setDate(0); // the last day
      let lastDayCount = time.getDate();
      // console.log(lastDayCount,'lastDayCount')
      for (let i = detailDay; i > 0; i--) {
        let status = 'next-month';
        let tmpTime = new Date(time.getFullYear(), time.getMonth(), lastDayCount - i + 1);
        if (min !== "" && min !== undefined && min !== null) {
          tmpTime <= new Date(min) && (status = 'dp-disabled');
        }
        if (max !== "" && max !== undefined && max !== null) {
          tmpTime >= new Date(max) && (status = 'dp-disabled');
        }
      console.log(111)
        arr.push({
          text: lastDayCount - i + 1,
          date: tmpTime,
          status: status
        })
      }

      time.setMonth(time.getMonth() + 2, 0);       // the last day of this month
      let datailDayCount = time.getDate();         // the last day
      // console.log(datailDayCount,'datailDayCount')
      time.setDate(1);                             // fix bug when month change
      let value = this.dp_value || this.stringify(nowServerDate);
        console.log(value,'value')

      for (let i = 1; i <= datailDayCount; i++) {
        let tmpTime = new Date(time.getFullYear(), time.getMonth(), i, new Date(value).getHours(), new Date(value).getMinutes(), new Date(value).getSeconds());
        // console.log(tmpTime,'tmpTime')

        let status = '';
        let text = i;
        let todays = this.stringify(new Date(nowServerDate.getFullYear(), nowServerDate.getMonth(), nowServerDate.getDate(), new Date(value).getHours(), new Date(value).getMinutes(), new Date(value).getSeconds()));
        if (this.stringify(tmpTime) === todays) {
          status = 'dp-today';
          text = '今天';
        }
        this.stringify(tmpTime) === value && (status = 'dp-selected');
        if (min !== "" && min !== undefined && min !== null) {
          tmpTime <= new Date(min) && (status = 'dp-disabled');
        }
        if (max !== "" && max !== undefined && max !== null) {
          tmpTime >= new Date(max) && (status = 'dp-disabled');
        }
        arr.push({
          text: text,
          date: tmpTime,
          status: status
        });
      }
      var j = 1;
      while (arr.length < this.dp_col * this.dp_row) {
        let status = 'next-month';
        let tmpTime = new Date(time.getFullYear(), time.getMonth() + 1, j);
        if (min !== "" && min !== undefined && min !== null) {
          tmpTime <= new Date(min) && (status = 'dp-disabled');
        }
        if (max !== "" && max !== undefined && max !== null) {
          tmpTime >= new Date(max) && (status = 'dp-disabled');
        }
        arr.push({
          text: j,
          date: tmpTime,
          status: status
        });
        j++;
      }
      this.dp_day = arr;
    },
    /*  月份的获取  */
    updateMonth: function () {
      let min = this.dtMin;
      let max = this.dtMax;
      for (let i = 0; i < 12; i++) {
        let status = '';
        let tmpTime = new Date(new Date(this.dp_now).getFullYear(), i, 1);
        new Date(this.dp_now).getMonth() === i && (status = 'dp-selected');
        if (min !== "" && min !== undefined && min !== null) {
          let minTime = new Date(new Date(min).getFullYear(), new Date(min).getMonth(), 1);
          tmpTime < minTime && (status = 'dp-disabled');
        }
        if (max !== "" && max !== undefined && max !== null) {
          let maxTime = new Date(new Date(max).getFullYear(), new Date(max).getMonth(), 1);
          tmpTime > maxTime && (status = 'dp-disabled');
        }
        this.dp_month[i].status = status;
      }
    },
    /*  年份的获取  */
    updateYear: function () {
      let str = this.dp_option.thisYear + "";
      let num = parseInt(str.substring(0, 3) + "0");
      this.dp_option.thisYear = num + " 年 ~ " + (num + 9);
      let arr = [];
      let min = this.dtMin;
      let max = this.dtMax;
      for (let i = num; i < num + 10; i++) {
        let status = '';
        new Date(this.dp_now).getFullYear() === i && (status = 'dp-selected');
        if (min !== "" && min !== undefined && min !== null) {
          let minTime = new Date(min).getFullYear();
          i < minTime && (status = 'dp-disabled');
        }
        if (max !== "" && max !== undefined && max !== null) {
          let maxTime = new Date(max).getFullYear();
          i > maxTime && (status = 'dp-disabled');
        }
        arr.push({
          years: i,
          text: i + "",
          status: status
        });
      }
      this.dp_year = arr;
    },
    dateClick: function (str) {
      this.dp_show.is_years = false;
      this.dp_show.is_months = false;
      this.dp_show.is_days = false;
      switch (str) {
        case 'is_years':
          this.dp_show.is_years = true;
          this.updateYear();
          break;
        case 'is_months':
          this.dp_show.is_months = true;
          this.updateMonth();
          break;
        default:
          this.dp_show.is_days = true;
      }
    },
    nextYear: function (flag) {
      if (this.dp_show.is_years) {
        this.dp_now.setFullYear(this.dp_now.getFullYear() + (flag * 10));
        this.updateYear();
      } else {
        this.dp_now.setFullYear(this.dp_now.getFullYear() + flag);
      }
      this.dp_now = new Date(this.dp_now);
      this.dp_option.thisYear = this.dp_now.getFullYear();
    },
    nextMonth: function (flag) {
      this.dp_now.setMonth(this.dp_now.getMonth() + flag);
      this.dp_now = new Date(this.dp_now);
    },
    pickDate: function (ind) {
      // console.log(ind,'aasda')
      this.dp_now = new Date(this.dp_day[ind].date);
      this.dp_value = this.stringify();
      // console.log(this.dp_value,'this.dp_value')

      if (!this.dtTime) {
        this.confirmDate();
      }
    },
    pickMonth: function (ind) {
      this.dp_now.setMonth(ind);
      this.dp_now = new Date(this.dp_now);
      this.dateClick("is_days");
    },
    pickYear: function (ind) {
      this.dp_now.setFullYear(this.dp_year[ind].years);
      this.dp_now = new Date(this.dp_now);
      this.dp_option.thisYear = this.dp_now.getFullYear();
      this.dateClick("is_months");
    },
    // when use the time
    getNowTime: function (str) {
      this.dp_option.timeChange = true;
      switch (str) {
        case "Hour":
          this.dp_option.thisHour = this.$refs.Hour.value;
          break;
        case "Minute":
          this.dp_option.thisMinute = this.$refs.Minute.value;
          break;
        case "Second":
          this.dp_option.thisSecond = this.$refs.Second.value;
          break;
      }
    },
    clearConfim: function () {
      this.closeDate();
      this.$emit('date-change', "");
    },
    confirmDate: function () {
      if (this.dp_option.timeChange) {
        this.dp_option.timeChange = false;
        this.dp_value = new Date(this.dp_value);
        this.dp_value.setHours(this.dp_option.thisHour);
        this.dp_value.setMinutes(this.dp_option.thisMinute);
        this.dp_value.setSeconds(this.dp_option.thisSecond);
        this.dp_now = this.dp_value;
        this.dp_value = this.stringify(this.dp_value);
      }
      this.dp_confirm_value = this.dp_value;
      this.closeDate();
      this.$emit('date-change', this.dp_confirm_value);
      // this.$emit('date-change', {$date:new Date(this.dp_confirm_value.replace(/-/g, '/')).toISOString()});
    },
    getNowDate: function () {
      this.dp_option.timeChange = false;
      this.dp_now = this.getSevertime();
      this.dp_option.thisHour = this.dp_now.getHours();
      this.dp_option.thisMinute = this.dp_now.getMinutes();
      this.dp_option.thisSecond = this.dp_now.getSeconds();
      this.dp_value = this.stringify();
      this.confirmDate();
    },
    editorDate: function () {
      if (this.dtEditable) {
        if (this.strDateTime(this.dp_confirm_value)) {
          this.dp_value = this.stringify(new Date(this.dp_confirm_value));
        }
        this.dp_confirm_value = this.dp_value;
        this.dp_now = this.parse(this.dp_value) || this.getSevertime();
        this.dp_option.thisHour = this.dp_now.getHours();
        this.dp_option.thisMinute = this.dp_now.getMinutes();
        this.dp_option.thisSecond = this.dp_now.getSeconds();
      }
    },
    strDateTime: function (str) {
      str = str.replace(/(^\s+|\s+$)/g, '');//去两边空格;
      if (str === '') {
        return false;
      }
      if (this.dtTime) {
        // 2003-12-05 12:12:12
        var reg = /^(\d{1,4})(-|\/)(\d{1,2})(-|\/)(\d{1,2}) (\d{1,2}):(\d{1,2}):(\d{1,2})$/;
        var r = str.match(reg);
        if (r === null) {
          return false;
        }
        if (r[3] > 12 || r[5] > 31 || r[6] > 24 || r[7] > 60 || r[8] > 60) {
          return false;
        }
        let strDate = new Date(r[1], r[3] - 1, r[5], r[6], r[7], r[8]);
        this.dp_confirm_value = this.dp_value = this.stringify(strDate);
        return true;
      } else {
        // 2012-01-12 || 2012/01/12
        var reg = /^(\d{1,4})(-|\/)(\d{1,2})(-|\/)(\d{1,2})$/;
        var r = str.match(reg);
        if (r === null) {
          return false;
        }
        if (r[3] > 12 || r[5] > 31) {
          return false;
        }
        let strDate = new Date(r[1], r[3] - 1, r[5]);
        this.dp_confirm_value = this.dp_value = this.stringify(strDate);
        return true;
      }
      return false;
    },
    stringify: function (time, format) {
      !time && (time = this.dp_now);
      !format && (format = this.dtFormat);

      if (this.dp_option.timeChange) {
        this.dp_option.timeChange = false;
        time.setHours(this.dp_option.thisHour);
        time.setMinutes(this.dp_option.thisMinute);
        time.setSeconds(this.dp_option.thisSecond);
      }

      let years = time.getFullYear();
      let months = time.getMonth() + 1;
      let days = time.getDate();
      let monthName = this.dp_month[time.getMonth()].cnText; //展示月份

      this.dp_option.thisMonth = time.getMonth();
      this.dp_option.thisYear = years;
      let dpMap = {
        YYYY: years,
        yyyy: years,
        MMName: monthName,
        MM: ('0' + months).slice(-2),
        M: months,
        DD: ('0' + days).slice(-2),
        dd: ('0' + days).slice(-2),
        D: days
      }
      if (this.dtTime) {
        let hours = time.getHours();
        dpMap.HH = dpMap.hh = ('0' + hours).slice(-2);
        dpMap.H = dpMap.h = hours;
        let minutes = time.getMinutes();
        dpMap.mm = ('0' + minutes).slice(-2);
        dpMap.m = minutes;
        let seconds = time.getSeconds();
        dpMap.ss = ('0' + seconds).slice(-2);
        dpMap.s = seconds;
        // YYYY  +  MM  +  DD  +  HH  +  mm  +  ss
        return format.replace(/Y+|y+|M+|D+|d+|H+|h+|m+|s+/g, function (str) {
          return dpMap[str];
        });
      } else {
        // YYYY  +  MM  +  DD
        return format.replace(/Y+|y+|M+|D+|d+/g, function (str) {
          return dpMap[str];
        });
      }
    },
    parse: function (str) {
      if (str === null || str === undefined) {
        return null;
      }
      let time = new Date(str);
      return isNaN(time.getTime()) ? null : time;
    },
    getSevertime: function () { //获取服务器当前时间
      return new Date();
      var xmlHttp = new XMLHttpRequest();
      if (!xmlHttp) {
        xmlHttp = new ActiveXObject("Microsoft.XMLHTTP");
      }
      xmlHttp.open("HEAD", location.href, false);
      xmlHttp.send();
      var severtime = new Date(xmlHttp.getResponseHeader("Date"));
      return severtime;
    },
    leave: function (e) {
      if (this.dp_is_show) {
        if (!this.$el.contains(e.target)) {
          this.closeDate();
        }
      }
    },
    update() {

      if (this.popper) {
        this.$nextTick(() => {
          this.popper.update();
          this.popperStatus = true;
        });
      } else {

        this.$nextTick(() => {
          this.popper = new Popper(this.$refs.reference, this.$refs.datetimePicker, {
            placement: this.placement,
            modifiers: {
              computeStyle: {
                gpuAcceleration: false
              },
              preventOverflow: {
                boundariesElement: 'window'
              }
            },
            onCreate: () => {
              this.resetTransformOrigin();
              this.$nextTick(this.popper.update());
            },
            onUpdate: () => {
              this.resetTransformOrigin();
            }
          })
        })
      }
    },
    resetTransformOrigin() {
      // 不判断，Select 会报错，不知道为什么
      if (!this.popper) return;
      let x_placement = this.popper.popper.getAttribute('x-placement');
      let placementStart = x_placement.split('-')[0];
      let placementEnd = x_placement.split('-')[1];
      const leftOrRight = x_placement === 'left' || x_placement === 'right';
      if (!leftOrRight) {
        this.popper.popper.style.transformOrigin = placementStart === 'bottom' || (placementStart !== 'top' && placementEnd === 'start') ? 'center top' : 'center bottom';
      }
    },
    destroyPopper() {
      if (this.popper) {
        setTimeout(() => {
          if (this.popper && !this.popperStatus) {
            this.popper.destroy();
            this.popper = null;
          }
          this.popperStatus = false;
        }, 300);
      }
    }
  },
  mounted: function () {
    this.initDate(this.value);
    this.dp_editable = this.dtEditable;
    this.dp_readonly = this.dtReadonly;
    this.dp_disable = this.dtDisable;
    this.dp_disable && (this.dp_readonly = true)
    this.dp_readonly && (this.dp_editable = false)
    document.addEventListener('click', this.leave, false);
  },
  beforeDestroy() {
    document.removeEventListener('click', this.leave, false);
  },
  destroy() {
    if (this.popper) {
      setTimeout(() => {
        if (this.popper && !this.popperStatus) {
          this.popper.destroy();
          this.popper = null;
        }
        this.popperStatus = false;
      }, 300);
    }
  }
}
</script>
