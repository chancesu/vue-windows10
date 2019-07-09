<template>
    <div class="calender-wrap">
        <div class="current-area">
            <p class="current-time">{{this.currentTime}}</p>
            <p class="current-day">{{this.currentDay}}</p>
        </div>
        <div class="calender-month">
            <div class="calender-handler">
                <button type="button" class="btn-month">{{this.monthBasic[0]}}년 {{this.monthBasic[1]}}월</button>
                <button type="button" class="btn-month-prev">이전 달</button>
                <button type="button" class="btn-month-next">다음 달</button>
            </div>
            <div class="calender-view">
                <div class="calender-top"><span v-for="day in this.week">{{day}}</span></div>
                <div class="calender-body">
                    <span v-for="(date,idx) in this.settingMontyly"
                    :class="{
                        other : idx < otherScope[0] || idx > 41-otherScope[1],
                        // today : this.monthBasic[0] === this.nowDate[0] && this.monthBasic[1] === this.nowDate[1] && idx === otherScope[0] + new Date().getDate()-1
                    }"><span>{{date}}</span></span>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    name: "calender",
    data (){
        return {
            currentTime : '',
            currentDay : '',
            currentDate : '',
            week : ['일', '월', '화', '수', '목', '금', '토'],
            settingMontyly : [],
            monthBasic : ['2019','7'],
            nowDate : [String(new Date().getYear() + 1900),String(new Date().getMonth() + 1)],
            otherScope : []
        }
    },
    methods :{
        updateTime(){
            let nowDate     = new Date();
            let hour        = nowDate.getHours();
            let minute      = nowDate.getMinutes();
            let sec         = nowDate.getSeconds();
            (hour < 12)? this.currentTime = "오전 " + hore : this.currentTime = "오후 " + String(hour-12);
            this.currentTime += `:${String(minute).padStart(2, '0')}:${String(sec).padStart(2, '0')}`;
            requestAnimationFrame(this.updateTime);
        },
        updateDate (){
            let nowDate     = new Date();
            let year        = nowDate.getYear() + 1900;
            let month       = nowDate.getMonth() + 1;
            let date        = nowDate.getDate();
            let day         = nowDate.getDay();
            let week        = ['일', '월', '화', '수', '목', '금', '토'];
            this.currentDay += `${year}년 ${month}월 ${date}일 ${this.week[day]}요일`;
            this.updateTime();
        },
        getCheckMonthly (y,m){
            const beforeLastDay = ( new Date( y, m-1, 0) ).getDate();
            const firstDay = new Date(y, m-1, 1).getDay();
            const lastDay = ( new Date( y, m, 0) ).getDate();
            let shortage = '';
            this.otherScope.push(firstDay);
            for(let prev = 0; prev < firstDay; prev++){
                this.settingMontyly.push(beforeLastDay-(firstDay-prev-1));
            }
            for(let now = 1; now <= lastDay; now++){
                this.settingMontyly.push(now);
            }
            if(this.settingMontyly.length < 7*6){
                let shortage = 7*6 - this.settingMontyly.length;
                this.otherScope.push(shortage);
                for(let next = 1; next <= shortage; next++){
                    this.settingMontyly.push(next);
                }
            }
        }
    },
    created(){
        this.updateDate();
    },
    mounted(){
        this.getCheckMonthly(...this.monthBasic);
    }
}
</script>

<style>
    .calender-wrap{
        position:fixed;
        bottom:40px;
        right:0px;
        border:1px solid #f00;
        width:360px;
        height:460px;
        padding:20px;
        background: rgba(0,0,0,.5);
        border:1px solid rgba(225,225,225,.2);
        border-width:1px 1px 0 1px;
        text-align:left;
    }
    .calender-wrap .current-time{
        font-size:45px;
        font-weight:300;
    }
    .calender-wrap .current-day{
        color:#429ce3;
        font-weight:400;
    }
    .calender-month{
        margin-top:30px;
    }
    .calender-month .calender-handler{
        position:relative;
        margin-bottom:10px;
        text-align:right;
    }
    .calender-month .calender-handler button{
       background:transparent;
       border:0;
       color:#fff;
    }
    .calender-month .calender-handler .btn-month{
        position:absolute;
        top:0;
        left:10px;
        font-size:20px;
        font-weight:300;
    }
    .calender-month .calender-handler .btn-month-prev,
    .calender-month .calender-handler .btn-month-next{
        overflow:hidden;
        display:inline-block;
        position:relative;
        width:35px;
        height:24px;
        text-indent:-1000em;
    }
    .calender-month .calender-handler .btn-month-prev:before,
    .calender-month .calender-handler .btn-month-next:before{
        content:"";
        position:absolute;
        top:0;
        left:0;
        right:0;
        text-indent:0;
        width:20px;
        height:11px;
        background:url("../../assets/images/taskbar/calendar-arrow.png") no-repeat 0 0;
        font-size:20px;
        text-align:center;
    }
    .calender-month .calender-handler .btn-month-next:before{
        transform: rotate(180deg)
    }
    .calender-month .calender-view span{
        line-height:40px;
    }
    .calender-month .calender-view .calender-top{
        display:grid;
        grid-template-columns: 52px 52px 52px 52px 52px 52px 52px;
        grid-template-rows: 40px;
    }
    .calender-month .calender-view .calender-top span{
        font-size:12px;
        text-align:center;
    }
    .calender-month .calender-view .calender-body{
        display:grid;
        grid-template-columns: 52px 52px 52px 52px 52px 52px 52px;
        grid-template-rows: 40px 40px 40px 40px 40px 40px;
    }
    .calender-month .calender-view .calender-body >span{
        font-size:20px;
        text-align:center;

    }
    .calender-month .calender-view .calender-body >span span{
        display:inline-block;
        width:38px;
        height:32px;
        vertical-align:1px;
        border:2px solid transparent;
        line-height:36px;
    }
    .calender-month .calender-view .calender-body >span:hover >span{
        border:2px solid rgba(225,225,225,.2);
    }
    .calender-month .calender-view .calender-body >span.other{
        opacity:.5;
    }
    .calender-month .calender-view .calender-body >span.today{
        border-color:#0078d7;
    }
    .calender-month .calender-view .calender-body >span.today span{
        position:relative;
        border-color:#0078d7;
        background:#0078d7;
    }
    .calender-month .calender-view .calender-body >span.today span:before{
        content:"";
        position:absolute;
        top:0;
        bottom:0;
        left:0;
        left:0;
        right:0;
        border:1px solid #000;
    }
</style>
