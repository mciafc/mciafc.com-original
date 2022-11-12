<template>
  <div class="body" :class="{ noscroll: bookingModalOpen }" id="About" ref="about">
    <BookForm @closebookingmodal="closeBookingModal" :bookingModalOpenProp="bookingModalOpen"></BookForm>
    <header class="header unselectable">
      <ul>
        <li>
          <p><a href="/#About">About Us</a></p>
        </li>
        <li>
          <p><a href="/#Talent">Talent Show</a></p>
        </li>
      </ul>
    </header>
    <div class="initialScreen">
      <div class="typographybg">
        <h1 class="bigtypography unselectable">WHAT IS THE AFC?</h1>
      </div>
      <div class="aboutbox selectable">
        <h1 class="clubname">AUDITORIUM FACILITIES CREW</h1>
        <br>
        <h2 class="gradient-text subheadingReposition">ABOUT US</h2>
        <p class="clubinfo">The AFC is responsible for working the more technical parts of the auditorium. We help out with assemblies, extracurricular activities, and more by setting up the stage lights, speakers and other equipment. We have about 20 crew members, if you need us for your event, click the button below to book!</p>
        <br>
        <br>
        <br>
        <button class="applybtn" @click="openBookingModal">BOOK US</button>
      </div>
      <div class="backgroundimg">
        <img src="./assets/board3.png" class="backgroundimg boardimgshift">
      </div>
      <div class="scrollReminder">
        <p>Scroll to see more</p>
      </div>
    </div>
    <div class="secondScreen">
      <div class="typographybg2">
        <h1 class="bigtypography2 unselectable">TALENT SHOW SIGNUPS</h1>
      </div>
      <div class="aboutbox2 selectable" id="Talent">
        <h1 class="clubname">TALENT SHOW REGISTRATION</h1>
        <br>
        <h2 class="gradient-text">DECEMBER 23</h2>
        <p class="clubinfo">Our normal Winter Assembly has been replaced with a Talent Show! Click the button below to
          audition for an act!</p>
        <button class="applybtn">GOT TALENT?</button>
      </div>
      <div class="backgroundimg2">
        <img src="./assets/drumkit.png" class="backgroundimg">
      </div>
    </div>
  </div>
</template>

<script>
import BookForm from "./components/BookForm.vue"

export default {
  name: 'App',
  components: {
    BookForm
  },
  data() {
    return {
      bookingModalOpen: false
    }
  },
  methods: {
    openBookingModal() {
      window.location.href = "#About"
      this.bookingModalOpen = true
    },
    closeBookingModal() {
      window.location.href = "#"
      this.bookingModalOpen = false; 
    }
  }, 
  watch: {
    bookingModalOpen: function() {
      if (this.bookingModalOpen) {
        return document.documentElement.style.overflow = 'hidden'
      } else {
        return document.documentElement.style.overflow = 'auto'
      }
    }   
  },
  mounted() {
      document.querySelectorAll("*").forEach((elem) => {
        elem.setAttribute('draggable', false)
        elem.addEventListener('dragstart', (event) => {
          event.preventDefault()
        })
      })
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Questrial&display=swap');

/* buttons.css */
button {
  padding: 10px 20px;
  background: linear-gradient(-45deg, rgb(229, 157, 22), #57acdc);
  background-size: 400% 400%;
  border: none;
  border-radius: 0.1rem;
  font-family: 'Questrial', sans-serif;
  font-size: 1.2rem;
  font-weight: 400;
  color: white;
  max-width: fit-content;
  z-index: 100;
  box-shadow: 6px 6px 5px rgba(0, 0, 0, 0.596);
  animation: reverse-gradient 1s forwards cubic-bezier(0.07, 0.5, 0.2, 1);
}

button:hover {
  animation: gradient 1s forwards cubic-bezier(0.07, 0.5, 0.2, 1);
  cursor: pointer;
  z-index: 100;
}

#email {
  display: none;
}

@keyframes gradient {
  0% {
    background-position: 0% 50%;
  }

  100% {
    background-position: 100% 50%;
  }
}

@keyframes reverse-gradient {
  0% {
    background-position: 100% 50%;
  }

  100% {
    background-position: 0% 50%;
  }
}

button:disabled {
  cursor: not-allowed;
}


/* contactpagestyles.css */
.contact {
  text-align: center;
  align-self: center;
}

.wrappercontactpg {
  display: flex;
  align-items: center;
  justify-content: center;
}

/* global.css */
:root {
  /* general random colors */
  --mciafcmagenta: #e91e63;
  --mciafcdarkmagenta: #c21858;
  --mciafcpurple: #9c2780;
  --mciafcdeeppurple: #572780;
  --mciafcblue: #272ab0;
  --mciafcnavy: #276880;
  --mciafcmint: #57dcbe;
  --mciafcgreen: #60c689;
  /* main palette */
  --mciafcorange: rgb(229, 157, 22);
  --mciafcsky: #57acdc;
  --mciafcgray: #313030;
  --mciafcblack: #1a1a1a;
}

a {
  font-family: 'Poppins', sans-serif;
  color: lightblue;
  text-decoration: none;
}

a:visited {
  color: lightblue;
}

html {
  scroll-behavior: smooth;
}


/* header.css */
li {
  float: left;
}

li p {
  display: block;
  color: white;
  text-align: center;
  padding: 16px;
  padding-top: 0;
  text-decoration: none;
}

ul {
  list-style-type: none;
  margin: 0;
  padding: 0;
  overflow: hidden;
}

.unselectable {
  -webkit-user-select: none;
  -webkit-touch-callout: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

.selectable {
  -webkit-user-select: auto !important;
  -webkit-touch-callout: auto !important;
  -moz-user-select: auto !important;
  -ms-user-select: auto !important;
  user-select: auto !important;
}

.header {
  position: fixed;
  background-color: var(--mciafcblack);
  width: 100%;
  padding-top: 10px;
  padding-bottom: -30px;
  z-index: 100;
}

/* initialscreen.css */

body {
  background-color: var(--mciafcblack);
  color: #FFF;
  font-family: 'Questrial', sans-serif;
  z-index: 2;
  overflow-x: hidden;
  margin-left: 0;
  margin-top: 0;
}

.clubname {
  color: var(--mciafcorange);
}

.backgroundimg {
  position: absolute;
  z-index: -1;
  width: 100%;
  height: 100%;
  margin: 0;
  overflow: hidden;
}

/* .boardimgshift {
  left: 200px;
  overflow-x: hidden;
  scale: 1.3
} */

.applybtn {
  position: absolute;
  margin: auto;
  left: 0;
  max-width: fit-content;
  right: 0;
  bottom: 50px;
}

.aboutbox {
  position: absolute;
  margin: 0;
  width: 400px;
  height: 600px;
  top: 15%;
  line-height: 1.2;
  left: 5%;
  background-color: var(--mciafcblack);
  z-index: 10;
  text-align: center;
  padding: 15px;
  box-shadow: 2px 2px rgba(0, 0, 0, 0.345);
}

.clubinfo {
  font-size: 25px;
}

.gradient-text {
  background-image: linear-gradient(-45deg, rgb(229, 157, 22), #57acdc);
  background-size: 200% 200%;
  background-clip: text;
  -webkit-background-clip: text;
  color: transparent;
}

.subheadingReposition {
  position: absolute;
  margin: auto;
  left: 0;
  right: 0;
  top: 150px;
}



/* Scroll Reminder */

.scrollReminder {
  overflow: hidden;
  position: absolute;
  bottom: 20px;
  background: linear-gradient(-45deg, rgb(229, 157, 22), #57acdc);
  background-position: 100% 50%;
  background-size: 200% 200%;
  z-index: 1;
  right: 30px;
  padding-left: 20px;
  padding-right: 20px;
  /* animation: scrollReminderPopIn 600ms forwards 5000ms;
  transform: translateX(120%); */
}


.bigtypography {
  /* position: absolute;
    top: 40%;
    right: 2%; */
  font-size: 100px;
  background-position: 50% 200%;
  background-image: linear-gradient(-45deg, rgb(229, 157, 22), #57acdc);
  background-size: 200% 200%;
  background-clip: text;
  -webkit-background-clip: text;
  color: transparent;
  text-shadow: 10px 5px 1px rgba(255, 255, 255);
}

.typographybg {
  position: absolute;
  top: 39%;
  right: 2%;
  padding-left: 20px;
  padding-right: 20px;
  width: fit-content;
  text-align: center;
  margin: 0;
  height: fit-content;
}



@keyframes scrollReminderPopIn {
  0% {
    transform: translateX(120%);
  }

  100% {
    transform: translateX(0%);
  }
}

/* secondscreen.css */
.aboutbox2 {
  position: absolute;
  margin: 0;
  width: 400px;
  height: 600px;
  top: 130%;
  right: 5%;
  background-color: var(--mciafcblack);
  z-index: 10;
  text-align: center;
  padding: 15px;
  line-height: 1.2;
  box-shadow: 2px 2px rgba(0, 0, 0, 0.345);
}

.backgroundimg2 {
  position: absolute;
  z-index: -1;
  width: 100%;
  top: 115%;
  height: 100%;
  margin: 0;
}

.applybtn2 {
  position: absolute;
  bottom: 50px;
  left: 33%;
}

.bigtypography2 {
  /* position: absolute;
    top: 40%;
    right: 2%; */
  font-size: 100px;
  background-position: 50% 200%;
  background-image: linear-gradient(-45deg, rgb(229, 157, 22), #57acdc);
  background-size: 200% 200%;
  background-clip: text;
  -webkit-background-clip: text;
  color: transparent;
  text-shadow: 10px 5px 1px rgba(255, 255, 255);
}

.typographybg2 {
  position: absolute;
  top: 147%;
  left: 2%;
  padding-left: 20px;
  padding-right: 20px;
  width: fit-content;
  max-width: 60%;
  text-align: center;
  margin: 0;
  height: fit-content;
}
</style>
