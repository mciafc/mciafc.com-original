<template>
    <div class="bigModal" v-if="viewingSpecs" ref="specsmodal">
        <h1 class="title">AUDITORIUM SPECS & FAQ</h1>
        <div class="container">
            <h2>Seating Capacity: 669 + Wheelchair accessibility</h2>
            <h2><a href="https://mciafc.com/equipment" target="__blank">Equipment Inventory</a></h2>
            <h2><a @click="notReadyYet">Stage Plan</a></h2>
            <h2><a @click="notReadyYet">Lighting & Sound Plot</a></h2>
            <h2><a href="https://www.tdsb.on.ca/Community/Permits/Dates-and-Times-of-Use" target="__blank">TDSB Permit Policy</a></h2>
            <p>We are under the TDSB, meaning we can not host any events during school hours. Please ensure your event falls within a timeframe where we are able to get a permit before booking.</p>
            <h2>Accessibility</h2>
            <p>Our auditorium is very accessible. The main entrances and the right side entrances are wheelchair accessible.</p>
            <!-- <h2>Storage</h2>
            <p>You can store set pieces in the basement under the stage.</p>
            <h2>Laundry Equipment</h2>
            <p>We do not have any laundry equipment.</p>
            <h2>Can I have a display in the lobby?</h2>
            <p>Yes, if your show is after school hours and is cleaned up before you leave.</p>
            <h2>Our show requires (insert technical need) can your venue provide this?</h2>
            <p>If it's not in our <a href="https://mciafc.com/equipment">equipment</a> spreadsheet, we cannot provide it.</p> -->
        </div>
        <button class="closebutton" @click="closeAnimation">CLOSE</button>
    </div>
</template>

<script>
export default {
    name: "SpecsModal",
    props: {
        viewingSpecs: Boolean
    },
    methods: {
        closeAnimation() {
            this.$refs.specsmodal.classList.add('backUp')
            setTimeout(this.emitClose, 700)
        },
        emitClose() {
            this.$emit('closeSpecsModal')
        },
        notReadyYet() {
            let title = "COMING SOON"
            let text = "We're still collecting this data. Please check back soon."
            this.$emit("notReadyYet", title, text)
        }
    },
    emits: ['closeSpecsModal', 'notReadyYet']
}
</script>

<style scoped>
.bigModal {
        width: 100%;
        height: 100%;
        position: fixed;
        background-color: #191919;
        z-index: 1000;
        overflow-y: scroll;
        transform: translateY(-100%);
        animation: dropDown 800ms forwards ease-in-out;
    }

    .backUp {
        animation: backUp 800ms forwards ease-in-out;
    }

    @keyframes backUp {
        0% {
            transform: translateY(0)
        }
        100% {
            transform: translateY(-100%)
        }
    }

    @keyframes dropDown {
        0% {
            transform: translateY(-100%);
        }
        100% {
            transform: translateY(0)
        }
    }

    .title {
        text-align: center;
    }
    .container {
        position: absolute;
        left: 5%;
        width: 60%;
    }
    .closebutton {
        position: absolute;
        margin: auto;
        left: 0;
        right: 0;
        bottom: 10%;
        scale: 1.5;
        font-weight: 600;
    }
    a:hover {
        cursor: pointer;
    }
</style>