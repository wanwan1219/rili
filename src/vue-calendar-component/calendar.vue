<style>
* {
  margin: 0;
  padding: 0;
}

.wh_content_all {
  font-family: -apple-system, BlinkMacSystemFont, 'PingFang SC', 'Helvetica Neue', STHeiti, 'Microsoft Yahei', Tahoma,
    Simsun, sans-serif;
  background-color: white;
  width: 100%;
  overflow: hidden;
  padding-bottom: 8px;
  color: black;
}

.wh_content {
  display: flex;
  flex-wrap: wrap;
  padding: 0 3% 0 3%;
  width: 100%;
  /* border-bottom: 1px solid #000; */
}

.wh_content > div{
  border:1px solid black;
  border-left: none;
  border-bottom:none;
}

.wh_content > div:nth-of-type(7n-6){
  border-left: 1px solid black;
}

/* .wh_content > div:nth-of-type(29){
  border-bottom: 1px solid #000
} */

.wh_content:first-child .wh_content_item {
  color: #ddd;
  font-size: 16px;
  height: 15vw;
  line-height: 15vw;
}

.wh_content_item {
  font-size: 15px;
  height: 10.3vw;
  line-height: 10.3vw;
  width: 13.4vw;
  text-align: center;
  color: black;
}


.wh_is_mark {
  width: 100%;
  height: 100%;
  line-height: 10.3vw;
  /* margin: auto; */
  /* margin-top: 0.65vw; */
  /* border-radius: 100px; */
  border: 1px solid blue;
  background: green;
  z-index: 2;
}

.wh_is_today {
  width: 100%;
  height: 100%;
  line-height: 10.3vw;
  /* margin: auto; */
  /* margin-top: 0.65vw; */
  background: pink;
  /* color: #0fc37c; */
  /* border-radius: 100px; */
  text-align: center;
}

.wh_top_changge {
  display: flex;
  color: black
}

.wh_top_changge li {
  display: flex;
  color: black;
  font-size: 18px;
  flex: 1;
  justify-content: center;
  align-items: center;
  height: 47px;
}

.wh_top_changge .wh_content_li {
  flex: 2.5;
}

.wh_jiantou1 {
  width: 12px;
  height: 12px;
  border-top: 2px solid black;
  border-left: 2px solid black;
  transform: rotate(-45deg);
}

.wh_jiantou1:active,
.wh_jiantou2:active {
  border-color: #ddd;
}

.wh_jiantou2 {
  width: 12px;
  height: 12px;
  border-top: 2px solid black;
  border-right: 2px solid black;
  transform: rotate(45deg);
}
.wh_next_day_show {
  color: #bfbfbf;
}
.wh_is_marked {
  background: red;
}
</style>
<template>
  <section>
    <div class="wh_content_all">
      <div class="wh_top_changge">
        <li @click="PreMonth">
          <div class="wh_jiantou1"></div>
        </li>
        <li class="wh_content_li">{{date_top}}</li>
        <li @click="NextMonth">
          <div class="wh_jiantou2"></div>
        </li>
      </div>
      <div class="wh_content">
        <div class="wh_content_item" v-for="tag in text_top">{{tag}}</div>
      </div>
      <div class="wh_content">
        <div class="wh_content_item" v-for="(item,index) in list" >
          <div v-bind:class="{ 
              wh_is_today: item.is_today,
              wh_is_marked:item.is_marked,
              wh_is_mark:item.is_mark,
              wh_next_day_show:(is_hide_otherday&&item.next_day_show)||item.other_month}">
            {{item.id}}
          </div>
          
        </div>
      </div>
      <input type="button" @click="markToday" value="点击签到"/>
    </div>
  </section>
</template>
<script>
export default {
  data() {
    return {
      text_top: ['一', '二', '三', '四', '五', '六', '日'],
      my_data: [],
      list: [],
      date_top: ''
    };
  },
  props: {
    mark_array: { default: '[]' },
    marked_arr: { default: '[]' },
    is_hide_otherday: { default: false }
  },
  created() {
    this.my_data = new Date();
  },
  methods: {
    clickDay: function(item, index) {
      if (item.other_month) {
        item.other_month < 0 ? this.PreMonth() : this.NextMonth();
      } else {
        if (!(this.is_hide_otherday && item.next_day_show)) {
          this.$emit('chose_day', item.date);
          for (var i = 0; i < this.list.length; i++) {
            if (i == index) {
              this.list[i].is_today = true;
            } else {
              this.list[i].is_today = false;
            }
          }
        }
      }
    },
    PreMonth: function() {
      this.my_data = this.getPreMonth(this.my_data);
      this.$emit('change_month', this.dateformat(this.my_data));
      this.getlist(this.my_data);
    },
    NextMonth: function() {
      this.my_data = this.getNextMonth(this.my_data);
      this.$emit('change_month', this.dateformat(this.my_data));
      this.getlist(this.my_data);
    },
    /**
     * 获取上一个月
     */
    getPreMonth: function(date) {
      var time_array = this.dateformat(date).split('/');
      var year = time_array[0]; //获取当前日期的年份
      var month = time_array[1]; //获取当前日期的月份
      var day = time_array[2]; //获取当前日期的日
      var days = new Date(year, month, 0);
      days = days.getDate(); //获取当前日期中月的天数
      var year2 = year;
      var month2 = parseInt(month) - 1;
      if (month2 == 0) {
        year2 = parseInt(year2) - 1;
        month2 = 12;
      }
      var day2 = day;
      var days2 = new Date(year2, month2, 0);
      days2 = days2.getDate();
      if (day2 > days2) {
        day2 = days2;
      }
      if (month2 < 10) {
        month2 = '0' + month2;
      }
      var t2 = year2 + '/' + month2 + '/' + day2;
      return new Date(t2);
    },
    /**
     * 获取下一个月
     */
    getNextMonth: function(date) {
      var arr = this.dateformat(date).split('/');
      var year = arr[0]; //获取当前日期的年份
      var month = arr[1]; //获取当前日期的月份
      var day = arr[2]; //获取当前日期的日
      var days = new Date(year, month, 0);
      days = days.getDate(); //获取当前日期中的月的天数
      var year2 = year;
      var month2 = parseInt(month) + 1;
      if (month2 == 13) {
        year2 = parseInt(year2) + 1;
        month2 = 1;
      }
      var day2 = day;
      var days2 = new Date(year2, month2, 0);
      days2 = days2.getDate();
      if (day2 > days2) {
        day2 = days2;
      }
      if (month2 < 10) {
        month2 = '0' + month2;
      }

      var t2 = year2 + '/' + month2 + '/' + day2;
      return new Date(t2);
    },
    getDaysInOneMonth: function(date) {
      //通过获取下月面0号的日期可以知道这个月有多少天
      var getyear = date.getFullYear();
      var getmonth = date.getMonth() + 1;
      getmonth = parseInt(getmonth, 10);
      var d = new Date(getyear, getmonth, 0);
      return d.getDate();
    },
    getMonthweek: function(date) {
      //获取本月第一天是星期几，然后在去向前空几个
      var getyear = date.getFullYear();
      var getmonth = date.getMonth() + 1;
      var date_one = new Date(getyear + '/' + getmonth + '/1');
      return date_one.getDay() == 0 ? 6 : date_one.getDay() - 1;
    },
    getlist: function(date) {
      //渲染出来当前list
      var mygetMonth = date.getMonth() + 1 < 10 ? '0' + (date.getMonth() + 1) : date.getMonth() + 1;
      this.date_top = date.getFullYear() + '年' + mygetMonth + '月';
      var array = [];
      
      for (var i = 0; i < this.getDaysInOneMonth(date); i++) {
        let sameData = [date.getFullYear() , mygetMonth , i+1];
        
        if (
          this.dateformat(new Date()) ==
          this.dateformat(new Date(date.getFullYear() + '/' + (date.getMonth() + 1) + '/' + (i + 1)))
        ) {
          array = array.concat({
            //如果当前这天是今天 is_today是true
            id: i + 1,
            date: date.getFullYear() + '/' + (date.getMonth() + 1) + '/' + (i + 1),
            is_today: true,
            is_mark: this.hasSameArray(this.mark_array , sameData),
            is_marked: this.hasSameArray(this.marked_arr , sameData),
            next_day_show:
              new Date(date.getFullYear() + '/' + (date.getMonth() + 1) + '/' + (i + 1)).getTime() -
                new Date().getTime() >
              0
          });
          this.$emit(
            'is_today',
            this.dateformat(new Date(date.getFullYear() + '/' + (date.getMonth() + 1) + '/' + (i + 1)))
          );
        } else {
          array = array.concat({
            id: i + 1,
            date: date.getFullYear() + '/' + (date.getMonth() + 1) + '/' + (i + 1),
            is_today: false,
            is_mark: this.hasSameArray(this.mark_array , sameData),
            is_marked: this.hasSameArray(this.marked_arr , sameData),
            next_day_show:
              new Date(date.getFullYear() + '/' + (date.getMonth() + 1) + '/' + (i + 1)).getTime() -
                new Date().getTime() >
              0
          });
        }
      }
      var array2 = [];
      var num = this.getDaysInOneMonth(this.getPreMonth(date)) - this.getMonthweek(date) + 1;
      for (var i = 0; i < this.getMonthweek(date); i++) {
        array2 = array2.concat({ other_month: -1, id: num + i });
      }
      array = array2.concat(array);
      var length_ = 7 - array.length % 7;
      if (length_ < 7) {
        for (let i = 0; i < length_; i++) {
          array.push({ other_month: 1, id: i + 1 });
        }
      }
      this.list = array;
    },
    dateformat: function(date) {
      return date.getFullYear() + '/' + (date.getMonth() + 1) + '/' + date.getDate();
    },
    hasSameArray(arr1 , arr2) {
      let isSame = false;
      for (var i = 0; i < arr1.length; i++) {
          if(arr1[i][0] == arr2[0]&&arr1[i][1] == arr2[1]&&arr1[i][2] == arr2[2]){
            isSame = true;
          }
       }
      return isSame
    },
    markToday(){
      console.log(this.list);
      console.log(this.mark_array);

      let todayArr=this.dateformat(this.my_data).split("/",3)
      let todayArr2=[];
      for(let i=0;i<todayArr.length;i++){
        todayArr2.push(Number(todayArr[i]));
      }
      console.log(todayArr2);

      for(let i=0; i<this.mark_array.length; i++){
        if(todayArr2[0]==this.mark_array[i][0] && todayArr2[1]==this.mark_array[i][1] && todayArr2[2]==this.mark_array[i][2]){
          console.log(this.dateformat(this.my_data))
          //判断今天是否在活动日期里 在的话点击按钮加is_marked
          for(let j=0 ;j<this.list.length; j++){
            if(this.list[j].date==this.dateformat(this.my_data)){
              this.list[j].is_marked=true;
              //签到成功，不能再次签到，并且向后台发送已签到日期
            }
          }
        }else{
          //不能签到
        }
      }
      
    },
  },
  mounted() {
    this.getlist(this.my_data);
  },
  watch: {
    mark_array(val, oldVal) {
      var list = this.list;
      for (var i = 0; i < list.length; i++) {
        list[i].is_mark = false;
        if (list[i].date) {
          for (var n = 0; n < val.length; n++) {
            if (list[i].id == val[n]) {
              list[i].is_mark = true;
            }
          }
        }
      }
      this.list = list;
    }
  }
};
</script>
