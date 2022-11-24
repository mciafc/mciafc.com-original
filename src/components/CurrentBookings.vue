<template>
    <div class="paper">
        <div v-if="shownGigs(gigs).length > 0">
            <div v-for="gig in shownGigs(gigs)" :key="gig._id">
                <h3><span style="text-decoration: underline; cursor: pointer;"
                        @click="showAdditionalEventInformation(gig)">{{ gig.gigName }}</span> - {{
                        dateRange(gig.gigStartDate, gig.gigEndDate) }}</h3>
            </div>
        </div>
        <div v-else>
            <h3>No upcoming events scheduled.</h3>
        </div>
    </div>
</template>

<script>
import io from "socket.io-client";
export default {
    name: "CurrentBookings",
    data() {
        return {
            socket: {},
            gigs: [],
        }
    },
    emits: ['moreInfo'],
    created() {
        this.socket = io("https://io.mciafc.com/gigs")
    },
    mounted() {
        this.socket.on("upcominggigs", data => {
            this.gigs = data
        })
    },
    computed: {
        dateRange() {
            return function (start, end) {
                let startString = new Date(start).toDateString()
                // let endString = new Date(end).toDateString()
                let startMinutes = new Date(start).getMinutes()
                let startHours = new Date(start).getHours()
                let endMinutes = new Date(end).getMinutes()
                let endHours = new Date(end).getHours()
                if (startMinutes < 10) {
                    startMinutes = `0${startMinutes}`
                }
                if (endMinutes < 10) {
                    endMinutes = `0${endMinutes}`
                }
                return `${startString} @ ${startHours}:${startMinutes} - ${endHours}:${endMinutes}`
            }
        },
        employeesNeeded() {
            return function (amountSpecified) {
                if (amountSpecified > -1) {
                    return `${amountSpecified}`
                }
                return "As many as possible."
            }
        },
        shownGigs() {
            return function (gigs) {
                let showGigs = []
                gigs.forEach((gig) => {
                    if (gig.showOnHomepage == true) {
                        console.log(showGigs.length)
                        if (showGigs.length + 1 <= 3) {
                            showGigs.push(gig)
                        }

                    }
                })
                return showGigs
            }
        }
    },
    methods: {
        showAdditionalEventInformation(gig) {
            this.$emit('moreInfo', gig)
        }
    }
}
</script>

<style scoped>

</style>