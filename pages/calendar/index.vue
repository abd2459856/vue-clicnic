<template>
    <div>
        <v-sheet tile height="54" class="d-flex">
            <v-btn icon class="ma-2" @click="$refs.calendar.prev()">
                <v-icon>mdi-chevron-left</v-icon>
            </v-btn>
            <v-spacer></v-spacer>
            <v-btn icon class="ma-2" @click="$refs.calendar.next()">
                <v-icon>mdi-chevron-right</v-icon>
            </v-btn>
        </v-sheet>

        <v-sheet>
            <v-row cols="12" :xs="12" :sm="12" :md="12" :lg="12" :xl="12">
                <v-col :cols="12" :xs="12" :sm="12" :md="12" :lg="12" :xl="12" class="text-center">
                    <v-toolbar-title v-if="$refs.calendar">
                        <b> {{ $refs.calendar.title }}</b>
                    </v-toolbar-title>
                </v-col>
                <v-col :cols="12" :xs="12" :sm="12" :md="12" :lg="12" :xl="12" class="text-center mt-1">
                </v-col>
            </v-row>
        </v-sheet>
        <v-sheet height="600">
            <v-calendar ref="calendar" v-model="value" :weekdays="weekday" :type="type" :events="events"
                :event-overlap-mode="mode" :event-overlap-threshold="30" :event-color="getEventColor"
                @click:event="showEvent" @click:date="showday" @change="getEvents"></v-calendar>
        </v-sheet>
    </div>
</template>
<script>
import axios from "axios";
export default {
    data: () => ({
        type: 'month',
        types: ['month', 'week', 'day', '4day'],
        mode: 'stack',
        modes: ['stack', 'column'],
        weekday: [0, 1, 2, 3, 4, 5, 6],
        weekdays: [
            { text: 'Sun - Sat', value: [0, 1, 2, 3, 4, 5, 6] },
            { text: 'Mon - Sun', value: [1, 2, 3, 4, 5, 6, 0] },
            { text: 'Mon - Fri', value: [1, 2, 3, 4, 5] },
            { text: 'Mon, Wed, Fri', value: [1, 3, 5] },
        ],
        value: '',
        events: [],
        colors: ['blue', 'indigo', 'deep-purple', 'cyan', 'green', 'orange', 'grey darken-1'],
        names: ['Meeting', 'Holiday', 'PTO', 'Travel', 'Event', 'Birthday', 'Conference', 'Party'],
        file: [],
    }),
    methods: {
        showday() {
            this.$router.push({
                path: "/",
                query: { Date: this.value },
            });
        },
        showEvent({ nativeEvent, event }) {
            console.log(event)
            const open = () => {
                this.selectedEvent = event
                this.selectedElement = nativeEvent.target
                // this.$router.push({
                //     path: "/",
                //     query: { Date: this.DateFormat(this.selectedEvent.end,'YYYY-MM-DD') },
                // });
            }
            if (this.selectedOpen) {
                this.selectedOpen = false
                requestAnimationFrame(() => requestAnimationFrame(() => open()))
            } else {
                open()
            }
            nativeEvent.stopPropagation()
        },
        async getEvents({ start, end }) {
            const events = []
            let { data } = await axios
                .get(`${process.env.api_url}/Welcome/get_rendezvous?ID_nut=`, {

                })
                .catch((err) => {
                    alert(err)
                });
            console.log(data.result)
            for (let i = 0; i < data.result.length; i++) {
                events.push({
                    name: data.result[i].Fisrtname + '' + data.result[i].Lastname,
                    start: data.result[i].Date_nut,
                    end: data.result[i].Date_nut,
                    color: data.result[i].Status_nut == 'มาถึง' || data.result[i].Status_nut == 'เข้าตรวจ' ? 'warning'
                        : data.result[i].Status_nut == 'ตรวจเสร็จ'
                            ? 'success'
                            : data.result[i].Status_nut == 'เลื่อนนัด'
                                ? 'purple'
                                : data.result[i].Status_nut == 'ยกเลิก'
                                    ? 'error'
                                    : 'cyan'
                })
            }

            this.events = events
        },
        getEventColor(event) {
            return event.color
        },
        rnd(a, b) {
            return Math.floor((b - a + 1) * Math.random()) + a
        },
    },
}
</script>