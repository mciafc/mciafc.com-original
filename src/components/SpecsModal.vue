<template>
    <div class="bigModal">
        <h1 class="title">AUDITORIUM SPECS</h1>
        <div class="container">
            <h2>Seating Capacity: 669</h2>
            <h2><a href="https://mciafc.com/equipment">Equipment</a></h2>
        </div>
    </div>
</template>

<script>
export default {
    name: "SpecsModal"
}
</script>

<style scoped>
    .bigModal {
        width: 100%;
        height: 100%;
        position: fixed;
        background-color: #191919;
        z-index: 1000;
    }

    .title {
        text-align: center;
    }
    .container {
        position: absolute;
        left: 5%;
    }
</style>