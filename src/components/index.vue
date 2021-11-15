<template>
    <div>
        <div class="fc-toolbar">
            <div class="fc-left">
                <el-button-group>
                    <el-button @click="month"> 月 </el-button>
                    <el-button @click="week"> 周 </el-button>
                    <el-button @click="today"> 今天 </el-button>
                </el-button-group>
            </div>
            <div class="fc-center">
                <el-button icon="el-icon-arrow-left" @click="prev" />
                <p class="title">
                    {{ title }}
                </p>
                <el-button icon="el-icon-arrow-right" @click="next" />
            </div>
            <div>
                <el-select
                    v-model="type"
                    style="margin-right: 20px"
                    @change="handleType"
                >
                    <el-option
                        v-for="item in options"
                        :key="item.value"
                        :label="item.label"
                        :value="item.value"
                    />
                </el-select>
                <el-button
                    class="search"
                    style="margin-right: 20px"
                    type="button"
                    @click="addEvent"
                >
                    新增
                </el-button>
                <el-button class="search" type="button" @click="search">
                    查询
                </el-button>
            </div>
        </div>
        <div v-if="showFullcalendar">加载数据中......</div>
        <FullCalendar
            v-else
            id="calendar"
            ref="fullCalendar"
            class="demo-app-calendar"
            :options="calendarOptions"
        >
            <template v-slot:eventContent="arg">
                <el-popover
                    placement="top-start"
                    title="标题"
                    width="200"
                    :visible-arrow="false"
                    trigger="click"
                >
                    <i class="title">{{ arg.event.title }}</i>
                    <el-button @click="more"> 更多 </el-button>
                    <div slot="reference" class="popper-content">
                        <span>{{ arg.timeText }}</span>
                        <i>{{ arg.event.title }}</i>
                        <i>{{ arg.event.title }}</i>
                    </div>
                </el-popover>
            </template>
            <template v-slot:dayCellContent="arg">
                {{ arg.dayNumberText }}
            </template>
            <template v-slot:resourceLabelContent="arg">
                {{ arg.resource.id }}
            </template>
        </FullCalendar>
    </div>
</template>
<script>
import FullCalendar from "@fullcalendar/vue";
import dayGridPlugin from "@fullcalendar/daygrid";
import timeGridPlugin from "@fullcalendar/timegrid";
import listPlugin from "@fullcalendar/list";
import interactionPlugin from "@fullcalendar/interaction";
import resourceTimelinePlugin from "@fullcalendar/resource-timeline";

let clickCount = 0;
let prev = ""; // 上一次点击的dom节点
export default {
    components: {
        FullCalendar, // make the <FullCalendar> tag available
    },
    data() {
        return {
            showFullcalendar: true,
            title: "",
            currentView: {},
            options: [
                { value: "timeline", label: "resource-timeline" },
                { value: "dategrid", label: "agenda" },
            ],
            type: "dategrid",
            calendarOptions: {
                locale: "zh",
                timeZone: "UTC",
                plugins: [
                    dayGridPlugin,
                    timeGridPlugin,
                    listPlugin,
                    resourceTimelinePlugin,
                    interactionPlugin,
                ],
                initialView: "dayGridMonth",
                resourceAreaWidth: 200,
                contentHeight: 600,
                slotMinWidth: 70,
                resourceOrder: "number",
                editable: true,
                dayMaxEvents: true, // allow "more" link when too many events
                eventDurationEditable: true, // 可以调整事件的时间
                selectable: true, // 日历格子可选择
                nowIndicator: true, // 现在的时间线显示
                eventDisplay: "block", // 争对全天的情况下，以块状显示
                headerToolbar: false, // 隐藏头部的导航栏
                selectMirror: false,
                displayEventEnd: true, // like 08:00 - 13:00
                eventTimeFormat: {
                    // like '14:30:00'
                    hour: "2-digit",
                    minute: "2-digit",
                    meridiem: false,
                    hour12: false, // 设置时间为24小时
                },
                events: [],
                eventColor: "#378006",
                allDayText: "全天",
                dateClick: this.handleDateClick,
                resourceAreaHeaderContent: "Rooms",
                resources: [
                    {
                        id: "number_1",
                        title: "asas",
                        number: 1,
                    },
                    {
                        id: "number_2",
                        title: "asas",
                        number: 2,
                    },
                    {
                        id: "number_3",
                        title: "asas",
                        number: 3,
                    },
                ],
                schedulerLicenseKey: "GPL-My-Project-Is-Open-Source",
                resourceLabelContent(arg) {
                    return {
                        html: `<div>id: ${arg.resource.id}</div><div>title: ${arg.resource.title}</div>`,
                    };
                },
                views: {
                    customTimeLineWeek: {
                        type: "resourceTimeline",
                        duration: { weeks: 1 },
                        slotDuration: { days: 1 },
                        buttonText: "Custom Week",
                        slotLabelFormat: {
                            weekday: "long",
                            // month: 'numeric',
                            // day: 'numeric',
                            omitCommas: true,
                        },
                    },
                    customTimeLineMonth: {
                        type: "resourceTimeline",
                        duration: { month: 1 },
                        slotLabelFormat: {
                            // month: 'numeric',
                            day: "numeric",
                            // omitCommas: true,
                        },
                    },
                    customGridWeek: {
                        type: "timeGridWeek",
                        dayHeaderFormat: {
                            weekday: "long",
                        },
                        slotLabelFormat: {
                            // 左侧时间格式
                            hour: "2-digit",
                            minute: "2-digit",
                            meridiem: "lowercase",
                            hour12: false, // false设置时间为24小时
                        },
                    },
                },
                // 切换视图调用的方法
                datesSet() {},
            },
            calendarApi: null,
            monthEvent: [
                {
                    id: "number_1",
                    resourceId: "number_1",
                    title: "event 1",
                    start: "2021-10-21",
                    color: "purple",
                },
                {
                    resourceId: "number_2",
                    id: "number_2",
                    title: "event 2",
                    start: "2021-10-22",
                    color: "purple",
                },
                { title: "event 3", start: "2021-10-23" },
                { title: "event 4", start: "2021-10-24" },
                { title: "event 5", start: "2021-10-21" },
                { title: "event 6", start: "2021-10-21", color: "purple" },
                { title: "event 7", start: "2021-10-21" },
                {
                    id: "number_3",
                    resourceId: "number_3",
                    title: "event 6",
                    start: "20211120",
                    end: "20211122",
                    color: "purple",
                    extendedProps: {
                        description: "asdasdasdasdasdasdasdasds",
                    },
                },
                {
                    id: 4,
                    title: "event 7",
                    start: "2021-11-22",
                    extendedProps: {
                        description: "444444444444",
                    },
                },
            ],
            weekEvent: [
                {
                    id: "number_1",
                    resourceId: "number_1",
                    title: "week_event",
                    start: "2021-11-11",
                    color: "purple",
                },
            ],
        };
    },
    mounted() {
        setTimeout(() => {
            this.showFullcalendar = false;
            this.$nextTick(() => {
                this.calendarApi = this.$refs.fullCalendar.getApi();
                this.title = this.calendarApi.view?.title;
                this.getDtata();
            });
        }, 2000);
    },
    watch: {
        // 切换视图显示不同的事件
        "calendarApi.view.type"(newVal) {
            console.log(newVal, "newVal");
            this.getDtata();
        },
    },
    methods: {
        getDtata() {
            setTimeout(() => {
                this.calendarOptions.events =
                    this.calendarApi.view?.type === "dayGridMonth"
                        ? this.monthEvent
                        : this.weekEvent;
            }, 200);
        },
        more() {},
        // 增加事件
        addEvent() {
            this.calendarApi.addEvent({
                id: "number_4",
                resourceId: "number_3",
                title: "event 11",
                start: "2021-11-28",
            });
            // this.calendarOptions.events.push({
            //     id: 'number_4',
            //     resourceId: 'number_3',
            //     title: 'event 11',
            //     start: '2021-11-28',
            // });
        },
        // 单击事件
        handleDateClick(e) {
            if (e.dateStr !== prev) {
                clickCount = 0;
            }
            clickCount += 1;
            prev = e.dateStr;
            setTimeout(() => {
                if (clickCount === 2) {
                    console.log("db click");
                } else if (clickCount === 1) {
                    console.log("one click");
                }
                clickCount = 0;
            }, 300);
        },
        prev() {
            this.calendarApi.prev();
            this.title = this.calendarApi.view?.title;
        },
        next() {
            this.calendarApi.next();
            this.title = this.calendarApi.view?.title;
        },
        today() {
            this.calendarApi.today();
            this.title = this.calendarApi.view?.title;
        },
        month() {
            if (this.type === "timeline") {
                this.calendarApi.changeView("customTimeLineMonth");
            } else {
                this.calendarApi.changeView("dayGridMonth");
            }
            this.calendarApi.today();
            this.title = this.calendarApi.view?.title;
        },
        week() {
            if (this.type === "timeline") {
                this.calendarApi.changeView("customTimeLineWeek");
            } else {
                this.calendarApi.changeView("customGridWeek");
            }
            this.calendarApi.today();
            this.title = this.calendarApi.view?.title;
        },
        day() {
            this.calendarApi.today();
            this.title = this.calendarApi.view?.title;
        },
        search() {
            this.calendarApi.changeView("dayGrid", {
                start: "2021-10-21",
                end: "2021-10-23",
            });
        },
        handleType() {
            if (this.type === "timeline") {
                this.calendarApi.changeView("customTimeLineMonth");
                this.calendarOptions.slotLabelFormat = null;
            } else {
                this.calendarApi.changeView("dayGridMonth");
            }
        },
    },
};
</script>

<style>
.demo-app {
    display: flex;
    min-height: 100%;
    font-family: Arial, Helvetica Neue, Helvetica, sans-serif;
    font-size: 14px;
}
.demo-app-sidebar {
    width: 300px;
    line-height: 1.5;
    background: #eaf9ff;
    border-right: 1px solid #d3e2e8;
}
.demo-app-sidebar-section {
    padding: 2em;
}
.demo-app-main {
    flex-grow: 1;
    padding: 3em;
}
.fc {
    /* the calendar root */
    max-width: 1100px;
    margin: 0 auto;
}
.fc-toolbar {
    width: 100%;
    margin: 30px auto;
    display: flex;
    flex: 1;
    justify-content: space-around;
    align-content: center;
}
.fc-center {
    display: flex;
    align-content: center;
}
.fc-center .title {
    font-size: 16px;
    padding: 0 15px;
    font-weight: 700;
}
</style>
