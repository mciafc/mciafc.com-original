<template>
    <div class="bookModal" :class="{ scrollable: modalScrollEnabled }" ref="bookModal" v-if="bookingModalOpenProp">
        <h1 v-if="bookingModalBookStage != 5">Book the AFC</h1>
        <p v-if="![2, 5].includes(bookingModalBookStage)">Fields marked with * are required. You'll be able to review your information before you submit.</p>
        <!-- Organizer Info -->
        <div class="OrganizerInfo booktext" v-if="bookingModalBookStage == 0">
            <h2>Organizer Information</h2>
            <p>Some information about you, so we can get in contact in case we need any extra details or you need to let us know
                about changes.</p>
            <h3>Name*</h3>
            <input type="text" name="Organizer Name" ref="organizerName" placeholder="Organizer/Your Name">
            <h3>Email*</h3>
            <input type="text" name="Organizer Email" ref="organizerEmail" placeholder="Organizer/Your Email">
            <h3>Phone Number</h3>
            <p>We only call or text if it's really needed.</p>
            <input type="text" name="Organizer Phone Number" ref="organizerPhoneNumber" placeholder="Organizer/Your Phone Number">
        </div>
        <!-- Organization Info -->
        <div class="OrganizationInfo booktext" v-if="bookingModalBookStage == 1">
            <h2>Organization Information</h2>
                <h3>Organization Name*</h3>
                <input type="text" name="Organization Name" ref="organizationName" placeholder="(e.g AFC, SAC, etc.)">
        </div>
        <!-- Event info -->
        <div class="EventInfo booktext" v-if="bookingModalBookStage == 2">
            <h2>Event Information</h2>
            <h3>Event Name*</h3>
            <input type="text" name="Event Name" ref="eventNameRef" placeholder="(e.g Terry Fox Assembly)">
            <h3>Event Start*</h3>
            <p>This should not include the time we will use to setup.</p>
            <input type="datetime-local" name="Event Start Date" ref="eventStartDate" class="dateselect">
            <h3>Event End*</h3>
            <p>This should not include the time we will use for teardown.</p>
            <input type="datetime-local" name="Event End Date" ref="eventEndDate" class="dateselect">
            <h3>Event Location</h3>
            <p>Where your event is being held at.</p>
            <input type="text" name="Event Location" ref="eventLocation" placeholder="(e.g Cafeteria, Library, Gym)">
        </div>
        <!-- Crew Info -->
        <div class="CrewInfo booktext" v-if="bookingModalBookStage == 3">
            <h2>Crew Information</h2>
            <h3>Crew Members Needed</h3>
            <p>The amount of crew members needed, we have around 20 including 3 execs. Set to 0 for as many as possible.</p>
            <input type="number" name="CrewMembersNeeded" max="21" min="0" value="0" ref="crewMembersNeeded">
            <h3>Paid Job</h3>
            <p>Tick the box if the crew members that will be present will be getting paid for their work. If you're an in-school
                organization, you should probably leave the box unticked.</p>
            <input type="checkbox" name="Paid Job" ref="paidJob">
        </div>
        <!-- Additional Info -->
        <div class="AdditionalInfo booktext" v-if="bookingModalBookStage == 4">
            <h2>Additional Information</h2>
            <p>Any additional information that you feel needs to be specified can go below.</p>
            <textarea name="Additional Info" ref="additionalInfo"
                placeholder="(e.g Sound equipment should be set up at the back of the auditorium), other useful things to put here are the types of equipment we will need, we've got plenty so just let us know and we'll get back and let you know if we have it."></textarea>
        </div>
        <!-- Verify -->
        <div class="verifyInfo booktext" v-if="bookingModalBookStage == 5">
            <h1>Verify your Info:</h1>
            <p>Click any of the headings to go back and edit</p>
            <h3 style="text-decoration: underline; cursor: pointer;" @click="startReview(0)">Organizer Info</h3>
            <p>Organizer Name: {{ organizerName }}</p>
            <p>Organizer Email: {{ organizerEmail }}</p>
            <p v-if="organizerPhoneNumber != 'Not provided'">Organizer Phone Number: {{ organizerPhoneNumber }}</p>
            <p>Organization Name: {{ organizationName }}</p>
            <h3 style="text-decoration: underline; cursor: pointer;" @click="startReview(2)">Event Info</h3>
            <p>Event Name: {{ eventName }}</p>
            <p>Takes Place: {{ dateRange(eventStartDate, eventEndDate) }}</p>
            <p>Event Location: {{ eventLocation }}</p>
            <h3 style="text-decoration: underline; cursor: pointer;" @click="startReview(3)">Crew Info</h3>
            <p>Crew Members Needed: {{ employeesNeeded(crewMembersNeeded) }}</p>
            <p>Paid Job: {{ paidJob }}</p>
            <h3 style="text-decoration: underline; cursor: pointer;" @click="startReview(4)" v-if="additionalInfo != 'No additional details specified.'">Additional Info (Click to show)</h3>
        </div>
        <br>
        <button class="continuebutton" @click="nextModalPage" v-if="!reviewMode" ref="continuebutton">Continue</button>
        <button class="continuebutton" @click="finishReviewing" v-else>Finish Reviewing This Section</button>
        <p style="color: red;" v-if="requiredFieldCheckFailed" class="fieldCheckText" ref="fieldCheckText">Please fill out all required fields</p>
        <p class="pagenumber">Page {{ bookingModalBookStage + 1 }} / 6</p>
    </div>
    <div class="darkenbackground" v-if="bookingModalOpenProp" @click="this.$emit('closebookingmodal')" :class="{ noscroll: bookingModalOpenProp }"></div>
</template>

<script>
import io from "socket.io-client";

export default {
    name: 'BookForm',
    data() {
        return {
            // State managers
            bookingModalOpen: false,
            bookingModalBookStage: 0, // 0 = organizer info, 1 = organization info, 2 = event info, 3 = crew info, 4 = additional info, 5 = confirmation window
            requiredFieldCheckFailed: false,
            reviewMode: false,
            modalScrollEnabled: false,

            // Socketio managers
            socket: {},

            // Booking Info
            // Organizer Info (Contact Info Stuff)
            organizerName: "", // req
            organizerEmail: "", // req
            organizerPhoneNumber: "", // not required

            // Organization Info
            organizationName: "", // req

            // Event Info
            eventName: "", // req
            eventStartDate: new Date(), // req
            eventEndDate: new Date(), // req
            eventLocation: "", // partially required, defaults to MCI Auditorium if left blank 

            // Crew information (Just extra info for us)
            membersNeeded: 0, // partially required, if left at 0 will show in the dashboard as "As many as possible"
            paidJob: false, // only usually paid by outside of school organizations like epo

            // Additional Info (big body of text)
            additionalInfo: "" // Will probably be edited by an exec
        }
    },
    props: {
        bookingModalOpenProp: Boolean
    },
    emits: ['closebookingmodal'],
    created() {
        this.socket = io("https://io.mciafc.com/gigs")
    },
    mounted() {
        this.socket.on("newGigRegistered", (newGig) => {
            let data = newGig
            console.log(data)
            if (data.success == true) {
                this.$emit("closebookingmodal")
                this.bookingModalBookStage = 0;
            }
        })
    },
    methods: {
        nextModalPage() {
            if (this.bookingModalBookStage === 0) {
                this.organizerName = this.$refs.organizerName.value
                this.organizerEmail = this.$refs.organizerEmail.value
                this.organizerPhoneNumber = this.$refs.organizerPhoneNumber.value
                if (!this.$refs.organizerPhoneNumber.value) {
                    this.organizerPhoneNumber = "Not provided"
                }
                if (!this.$refs.organizerName.value || !this.$refs.organizerEmail.value) {
                    this.requiredFieldCheckFailed = true
                } else {
                    this.bookingModalBookStage++
                    this.requiredFieldCheckFailed = false
                    return
                }
            } else
            if (this.bookingModalBookStage === 1) {
                this.organizationName = this.$refs.organizationName.value
                if (!this.$refs.organizationName.value) {
                    this.requiredFieldCheckFailed = true
                } else {
                    this.bookingModalBookStage++
                    this.requiredFieldCheckFailed = false
                    return
                }
            } else
            if (this.bookingModalBookStage === 2) {
                this.eventName = this.$refs.eventNameRef.value
                this.eventStartDate = this.$refs.eventStartDate.value
                this.eventEndDate = this.$refs.eventEndDate.value
                this.eventLocation = this.$refs.eventLocation.value
                console.log(this.$refs.eventNameRef.value)
                console.table({startDate: this.eventStartDate, endDate: this.eventEndDate})
                if (!this.$refs.eventLocation.value) {
                    this.eventLocation = "MCI Auditorium"
                }
                if (!this.$refs.eventNameRef.value || !this.$refs.eventStartDate.value.toString() || !this.$refs.eventEndDate.value.toString()) {
                    this.requiredFieldCheckFailed = true
                } else {
                    this.bookingModalBookStage++
                    this.requiredFieldCheckFailed = false
                    return
                }
            } else
            if (this.bookingModalBookStage === 3) {
                this.crewMembersNeeded = this.$refs.crewMembersNeeded.value
                this.paidJob = this.$refs.paidJob.checked
                console.log(this.$refs.paidJob.checked)
                if (!this.$refs.crewMembersNeeded) {
                    this.requiredFieldCheckFailed = true
                } else {
                    this.bookingModalBookStage++
                    this.requiredFieldCheckFailed = false
                    return
                }
            } else 
            if (this.bookingModalBookStage === 4) {
                this.additionalInfo = this.$refs.additionalInfo.value
                if (!this.$refs.additionalInfo.value) {
                    this.additionalInfo = "No additional details specified."
                }
                this.bookingModalBookStage++
                this.requiredFieldCheckFailed = false
                this.modalScrollEnabled = true
                return
            } else
            if (this.bookingModalBookStage === 5) {
                this.$refs.continuebutton.disabled = true;
                let body = {
                    "organizerName": this.organizerName,
                    "organizerContactEmail": this.organizerEmail,
                    "organizerContactNumber": this.organizerPhoneNumber,
                    "organizationName": this.organizationName,
                    "gigName": this.eventName,
                    "gigStartDate": this.eventStartDate,
                    "gigEndDate": this.eventEndDate,
                    "registeredByOrganizer": true,
                    "employeesNeeded": this.crewMembersNeeded,
                    "paidJob": this.paidJob,
                    "additionalInformation": this.additionalInfo
                }
                this.socket.emit("post", body)

                // fetch("https://api.mciafc.com/gigs/schedule", {
                //     method: "POST",
                //     headers: { "Content-Type": "application/json" },
                //     body: JSON.stringify(body)
                // }).then(res => res.json())
                // .then(data => {
                //     console.log(data)
                //     if (data.success == true) {
                //         this.$emit("closebookingmodal")
                //         this.bookingModalBookStage = 0;
                //     }
                // })
            }
        },
        startReview(page) {
            this.bookingModalBookStage = page;
            this.modalScrollEnabled = false
            this.reviewMode = true
        },
        finishReviewing() {
            if (this.bookingModalBookStage === 0) {
                this.organizerName = this.$refs.organizerName.value
                this.organizerEmail = this.$refs.organizerEmail.value
                this.organizerPhoneNumber = this.$refs.organizerPhoneNumber.value
                if (!this.$refs.organizerPhoneNumber.value) {
                    this.organizerPhoneNumber = "Not provided"
                }
                if (!this.$refs.organizerName.value || !this.$refs.organizerEmail.value) {
                    this.requiredFieldCheckFailed = true
                } else {
                    this.bookingModalBookStage = 1
                    this.requiredFieldCheckFailed = false
                    return
                }
            } else
            if (this.bookingModalBookStage === 1) {
                this.organizationName = this.$refs.organizationName.value
                if (!this.$refs.organizationName.value) {
                    this.requiredFieldCheckFailed = true
                } else {
                    this.bookingModalBookStage = 5
                    this.modalScrollEnabled = true
                    this.reviewMode = false
                    this.requiredFieldCheckFailed = false
                    return
                }
            } else
            if (this.bookingModalBookStage === 2) {
                this.eventName = this.$refs.eventNameRef.value
                this.eventStartDate = this.$refs.eventStartDate.value
                this.eventEndDate = this.$refs.eventEndDate.value
                this.eventLocation = this.$refs.eventLocation.value
                console.log(this.$refs.eventNameRef.value)
                console.table({startDate: this.eventStartDate, endDate: this.eventEndDate})
                if (!this.$refs.eventLocation.value) {
                    this.eventLocation = "MCI Auditorium"
                }
                if (!this.$refs.eventNameRef.value || !this.$refs.eventStartDate.value.toString() || !this.$refs.eventEndDate.value.toString()) {
                    this.requiredFieldCheckFailed = true
                } else {
                    this.bookingModalBookStage = 5
                    this.modalScrollEnabled = true
                    this.reviewMode = false
                    this.requiredFieldCheckFailed = false
                    return
                }
            } else
            if (this.bookingModalBookStage === 3) {
                this.crewMembersNeeded = this.$refs.crewMembersNeeded.value
                this.paidJob = this.$refs.paidJob.checked
                console.log(this.$refs.paidJob.checked)
                if (!this.$refs.crewMembersNeeded) {
                    this.requiredFieldCheckFailed = true
                } else {
                    this.bookingModalBookStage = 5
                    this.modalScrollEnabled = true
                    this.reviewMode = false
                    this.requiredFieldCheckFailed = false
                    return
                }
            } else 
            if (this.bookingModalBookStage === 4) {
                this.additionalInfo = this.$refs.additionalInfo.value
                if (!this.$refs.additionalInfo.value) {
                    this.additionalInfo = "No additional details specified."
                }
                this.bookingModalBookStage = 5
                this.modalScrollEnabled = true
                this.reviewMode = false
                this.requiredFieldCheckFailed = false
                this.modalScrollEnabled = true
                return
            }
        }
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
                if (amountSpecified > 0) {
                    return `${amountSpecified}`
                }
                return "As many as possible."
            }
        },
    }
}
</script>

<style scoped>

    @keyframes blur {
      0% {
        backdrop-filter: blur(0px);
      }

      100% {
        backdrop-filter: blur(8px);
      }
    }

    @keyframes fade-in {
      0% {
        opacity: 0;
      }

      100% {
        opacity: 1;
      }
    }

    /* @keyframes fade-out {
      0% {
        opacity: 1;
      }
  
      100% {
        opacity: 0;
      }
    } */

    input {
        padding: 10px;
        font-size: 13px;
        border-radius: 0.5rem;
    }
    textarea {
        padding: 10px;
        font-size: 13px;
        resize: none;
        height: 200px;
        width: 80%;
    }

    .bookModal {
        box-shadow:.8rem .8rem 1.4rem #151515, 
                   -.2rem -.2rem 1.8rem #272727;
        animation: fade-in 300ms forwards ease-out;
        position: absolute;
        margin: auto;
        left: 0;
        right: 0;
        top: 0;
        bottom: 0;
        text-align: center;
        height: 750px;
        width: 400px;
        background-color: #191919;
        font-size: 16px;
        z-index: 20000000;
        padding: 20px;
        padding-left: 75px;
        padding-right: 75px;
        border-radius: 3rem;
        overflow: hidden;
    }

    .booktext {
        position: relative;
        margin-top: auto;
        line-height: 0.9;
    }

    .darkenbackground {
        z-index: 10000;
        width: 100%;
        height: 100%;
        background-color: #19191980;
        animation: blur 300ms forwards ease-out;
        opacity: 1;
        position: absolute;
        margin: 0;
        top: 0;
        left: 0;
    }

    .scrollable {
        overflow-y: scroll !important;
    }

    .continuebutton {
        position: absolute;
        margin: auto;
        left: 0;
        right: 0;
        bottom: 80px;
    }
    .pagenumber {
        position: absolute;
        margin: auto;
        left: 0;
        right: 0;
        bottom: 20px;
    }
    .fieldCheckText {
        position: absolute;
        margin: auto;
        left: 0;
        right: 0;
        bottom: 120px;
    }
</style>