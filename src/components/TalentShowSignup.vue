<template>
    <div class="bookModal" :class="{ scrollable: modalScrollEnabled }" ref="bookModal" v-if="talentModalOpenProp">
        <h1 v-if="talentModalPage < 4">Register Your Act</h1>
        <p v-if="![2, 4, 5].includes(talentModalPage)">Fields marked with * are required. You'll be able to review
            your information before you submit.</p>
        <!-- Organizer Info -->
        <div class="OrganizerInfo booktext" v-if="talentModalPage == 0">
            <h2>Organizer Information</h2>
            <p>Some information about you, so we can get in contact in case we need any extra details or you need to let
                us know
                about changes.</p>
            <h3>Club?</h3>
            <p>Are you registering on behalf of a club? Leave blank if you're not.</p>
            <input type="checkbox" name="Is Club?" ref="isClub" v-model="isClub">
            <h3><span v-if="this.isClub">Club</span><span v-else>Your</span> Name*</h3>
            <input type="text" name="Organizer Name" ref="organizerName" placeholder="Your name or Club name" v-model="organizerName">
            <h3>Email*</h3>
            <p>We will use this to contact you about your act, tech rehearsals, and more.</p>
            <input type="text" name="Organizer Email" ref="organizerEmail" placeholder="Organizer/Your Email"
                v-model="organizerEmail">
        </div>
        <!-- Act Info -->
        <div class="ActInfo booktext" v-if="talentModalPage == 1">
            <h2>Act Information</h2>
            <h3>Act Name*</h3>
            <input type="text" name="Act Name" ref="actName" v-model="this.actName" placeholder="e.g Mystical Magic Show">
            <h3>Act Length (minutes)*</h3>
            <input type="number" name="Act Length" max="10" ref="actLength" v-model="this.actLength">
            <h3 v-if="!isClub">Act Members*</h3>
            <p v-if="!isClub">How people will be participating in your act</p>
            <input v-if="!isClub" type="number" name="Act Members" ref="actMembers" max="30" :value="this.actMembers">
            <h3>Act Description*</h3>
            <p>A short description about your act.</p>
            <input type="text" name="Act Description" ref="actDescription" :value="this.actDescription" placeholder="e.g I will perform some crazy magic!">
        </div>
        <!-- Equipment info -->
        <div class="EventInfo booktext" v-if="talentModalPage == 2">
            <h2>Equipment</h2>
            <p>What equipment does your act need?</p>
            <h3>Microphones*</h3>
            <input type="number" ref="mics" v-model="actEquipment.mics" max="4" min="0">
            <p style="color: red;" v-if="this.actEquipment.mics > 2"><span>Some mics will be wired.</span></p>
            <h3>Close-Up Camera</h3>
            <p>We will either setup a camera on-stage or have a handheld camera on-stage.</p>
            <input type="checkbox" ref="camera" v-model="actEquipment.camera">
            <h3>Spotlight (Followspot)</h3>
            <input type="checkbox" ref="followspot" v-model="actEquipment.followspot">
            <h3>Other Equipment</h3>
            <p><a href="https://mciafc.com/equipment" target="_blank">View all our equipment</a></p>
            <textarea type="text" name="Other Equipment" ref="other" placeholder="e.g Need stage monitors"
                v-model="actEquipment.other" class="otherEquipment"></textarea>
        </div>
        <!-- Additional Info -->
        <div class="AdditionalInfo booktext" v-if="talentModalPage == 3">
            <h2>Additional Information</h2>
            <p>Any additional information that you feel needs to be specified can go below.</p>
            <textarea name="Additional Info" ref="additionalInfo" v-model="this.additionalInfo"
                placeholder="Some examples of things you may want to put here are: Custom lighting arrangements, if you need music playing."></textarea>
        </div>
        <!-- Verify -->
        <div class="verifyInfo booktext" v-if="talentModalPage == 4">
            <h1>Verify your Info:</h1>
            <p>Click any of the headings to go back and edit</p>
            <h2 style="text-decoration: underline; cursor: pointer;" @click="startReview(0)">Organizer Info</h2>
            <p><b>Organizer Name:</b> {{ organizerName }}</p>
            <p><b>Organizer Email:</b> {{ organizerEmail }}</p>
            <h2 style="text-decoration: underline; cursor: pointer;" @click="startReview(1)">Act Info</h2>
            <p><b>Act Name:</b> {{ actName }}</p>
            <p><b>Act Length:</b> {{ actLength }}</p>
            <p v-if="!isClub"><b>Act Members:</b> {{ actMembers }}</p>
            <p><b>Act Description:</b> {{ actDescription }}</p>
            <h2 style="text-decoration: underline; cursor: pointer;" @click="startReview(2)">Equipment</h2>
            <p><b>Mics:</b> {{ actEquipment.mics }}</p>
            <p><b>Close-up Camera:</b> {{ actEquipment.camera }}</p>
            <p><b>Followspot:</b> {{ actEquipment.followspot }}</p>
            <p style="font-weight: 800; cursor: pointer;" @click="startReview(2)"><a>Other (Click to show)</a></p>
            <h2 style="text-decoration: underline; cursor: pointer;" @click="startReview(3)">Additional Info (Click to
                show)</h2>
        </div>
        <div class="confirmedAct" v-if="talentModalPage == 5">
            <h1>Signed up!</h1>
            <p>We will email you soon with your audition date, as well as any concerns we may have regarding equipment.</p>
        </div>
        <br>
        <a class="goback unselectable" v-if="talentModalPage < 4 && talentModalPage != 0" @click="goBack">←</a>
        <button class="continuebutton" @click="nextModalPage" v-if="!reviewMode"  v-show="talentModalPage != 5" ref="continuebutton">Continue</button>
        <button class="continuebutton" @click="finishReviewing" v-else>Finish Reviewing This Section</button>
        <button class="continuebutton" v-if="talentModalPage == 5" @click="closeModalAnimation">Done</button>
        <p style="color: red;" v-if="requiredFieldCheckFailed" class="fieldCheckText" ref="fieldCheckText">Please fill
            out all required fields</p>
        <p class="pagenumber" v-if="talentModalPage < 4">Page {{ talentModalPage + 1 }} / 4</p>
    </div>
    <div class="darkenbackground" ref="darkenbackground" v-if="talentModalOpenProp" @click="closeModalAnimation"
        :class="{ noscroll: talentModalOpenProp }"></div>
</template>

<script>
import io from "socket.io-client";

export default {
    name: 'BookForm',
    data() {
        return {
            // State managers
            talentModalOpen: false,
            talentModalPage: 0, // 0 = organizer info, 1 = act info, 2 = act equipment, 3 = additional info, 4 = confirmation window
            requiredFieldCheckFailed: false,
            reviewMode: false,
            modalScrollEnabled: false,

            // Socketio managers
            socket: {},

            // Booking Info
            // Organizer Info
            organizerName: "",
            organizerEmail: "",
            isClub: false,

            // Act Info
            actName: "",
            actLength: 0,
            actMembers: 1,
            actDescription: "",
            actEquipment: {
                mics: 0,
                camera: false,
                followspot: true,
                other: ""
            },

            // Additional Info
            additionalInfo: ""
        }
    },
    props: {
        talentModalOpenProp: Boolean
    },
    emits: ['closetalentmodal'],
    created() {
        this.socket = io("https://io.mciafc.com/talent")
    },
    mounted() {
        this.socket.on("newActRegistered", (newGig) => {
            let data = newGig
            console.log(data)
            if (data.success == true) {
                this.talentModalPage = 5;
                // Reset data
                this.organizerName = ""
                this.organizerEmail = ""
                this.actName = ""
                this.actLength = 0
                this.actMembers = 1
                this.actEquipment = {}
                this.additionalInfo = ""
                this.isClub = false,
                this.actDescription = ""
            }
        })
    },
    methods: {
        closeModalAnimation() {
            this.$refs.bookModal.classList.add('fade-out')
            this.$refs.darkenbackground.classList.add('unblur')
            setTimeout(this.closeModalEvent, 100)
        },
        goBack() {
            if (this.talentModalPage - 1 > -1) {
                this.talentModalPage--
            }
        },
        closeModalEvent() {
            this.talentModalPage = 0;
            this.$emit('closetalentmodal')
        },
        nextModalPage() {
            if (this.talentModalPage === 0) {
                this.organizerName = this.$refs.organizerName.value
                this.organizerEmail = this.$refs.organizerEmail.value
                if (!this.$refs.organizerName.value || !this.$refs.organizerEmail.value) {
                    this.requiredFieldCheckFailed = true
                } else {
                    this.talentModalPage++
                    this.requiredFieldCheckFailed = false
                    return
                }
            } else
                if (this.talentModalPage === 1) {
                    this.actName = this.$refs.actName.value
                    this.actLength = this.$refs.actLength.value
                    this.actDescription = this.$refs.actDescription.value
                    if (this.isClub) {
                        this.actMembers = -1
                    } else {
                        this.actMembers = this.$refs.actMembers.value
                    }
                    if (!this.$refs.actName.value || !this.$refs.actLength.value || !this.$refs.actDescription.value || !this.actMembers) {
                        this.requiredFieldCheckFailed = true
                    } else {
                        this.talentModalPage++
                        this.requiredFieldCheckFailed = false
                        return
                    }
                } else
                    if (this.talentModalPage === 2) {
                        if (!this.$refs.other.value) {
                            this.actEquipment.other = "No additional equipment specified."
                        }
                        if (!this.$refs.mics.value) {
                            this.requiredFieldCheckFailed = true
                        } else {
                            this.talentModalPage++
                            this.requiredFieldCheckFailed = false
                            return
                        }
                    } else
                        if (this.talentModalPage === 3) {
                            this.additionalInfo = this.$refs.additionalInfo.value
                            if (!this.$refs.additionalInfo.value) {
                                this.additionalInfo = "No additional details specified."
                            }
                            this.talentModalPage++
                            this.requiredFieldCheckFailed = false
                            this.modalScrollEnabled = true
                            return
                        } else
                            if (this.talentModalPage === 4) {
                                this.$refs.continuebutton.disabled = true;
                                let body = {
                                    "organizerName": this.organizerName,
                                    "organizerEmail": this.organizerEmail,
                                    "isClub": this.isClub,
                                    "actName": this.actName,
                                    "actLength": this.actLength,
                                    "actMembers": this.actMembers,
                                    "actDescription": this.actDescription,
                                    "actEquipment": this.actEquipment,
                                    "additionalInfo": this.additionalInfo
                                }
                                this.socket.emit("post", body)
                            }
        },
        startReview(page) {
            this.talentModalPage = page;
            this.modalScrollEnabled = false
            this.reviewMode = true
        },
        finishReviewing() {
            if (this.talentModalPage === 0) {
                this.organizerName = this.$refs.organizerName.value
                this.organizerEmail = this.$refs.organizerEmail.value
                if (!this.$refs.organizerName.value || !this.$refs.organizerEmail.value) {
                    this.requiredFieldCheckFailed = true
                } else {
                    this.talentModalPage = 4
                    this.reviewMode = false
                    this.requiredFieldCheckFailed = false
                    return
                }
            } else
                if (this.talentModalPage === 1) {
                    this.actName = this.$refs.actName.value
                    this.actLength = this.$refs.actLength.value
                    this.actDescription = this.$refs.actDescription.value
                    if (this.isClub) {
                        this.actMembers = -1
                    } else {
                        this.actMembers = this.$refs.actMembers.value
                    }
                    if (!this.$refs.actName.value || !this.$refs.actLength.value || !this.$refs.actDescription.value || !this.actMembers) {
                        this.requiredFieldCheckFailed = true
                    } else {
                        this.talentModalPage = 4
                        this.reviewMode = false
                        this.requiredFieldCheckFailed = false
                        return
                    }
                } else
                    if (this.talentModalPage === 2) {
                        if (!this.$refs.other.value) {
                            this.actEquipment.other = "No additional equipment specified."
                        }
                        if (!this.$refs.mics.value) {
                            this.requiredFieldCheckFailed = true
                        } else {
                            this.talentModalPage = 4
                            this.reviewMode = false
                            this.requiredFieldCheckFailed = false
                            return
                        }
                    } else
                        if (this.talentModalPage === 3) {
                            this.additionalInfo = this.$refs.additionalInfo.value
                            if (!this.$refs.additionalInfo.value) {
                                this.additionalInfo = "No additional details specified."
                            }
                            this.talentModalPage = 4
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

@keyframes unblur {
    0% {
        backdrop-filter: blur(8px);
    }

    100% {
        backdrop-filter: blur(0px);
    }
}

@keyframes fade-out {
    0% {
        opacity: 1;
    }

    100% {
        opacity: 0;
    }
}

.fade-out {
    animation: fade-out 100ms forwards ease-out !important;
}

.unblur {
    animation: unblur 100ms forwards ease-out !important;
}

input {
    font-family: "Poppins", sans-serif;
    padding: 7px;
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

.otherEquipment {
    height: 70px !important;
}

.verifyInfo p {
    font-size: 18px;
}

.bookModal {
    box-shadow: .8rem .8rem 1.4rem #151515,
        -.2rem -.2rem 1.8rem #222222;
    animation: fade-in 300ms forwards ease-out;
    position: fixed;
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
    animation: blur 200ms forwards ease-out;
    opacity: 1;
    position: fixed;
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

.goback {
    position: absolute;
    margin: auto;
    left: 150px;
    font-size: 2rem;
    bottom: 60px;
}

.goback:hover {
    cursor: pointer;
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