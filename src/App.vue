<template>
  <div class="body" :class="{ noscroll: bookingModalOpen }" id="About" ref="about">
    <BookForm @closebookingmodal="closeBookingModal" :bookingModalOpenProp="bookingModalOpen"></BookForm>
    <TalentModal @closetalentmodal="closeTalentModal" :talentModalOpenProp="talentModalOpen" />
    <InfoModal @closeinfomodal="closeInfoModal" :infoModalOpenProp="infoModalData.open" :headerProp="infoModalData.header" :textProp="infoModalData.text" />
    <header class="header unselectable">
      <ul>
        <div class="navbox">
          <li>
            <p><a class="about" href="/#About">About Us</a></p>
          </li>
          <li>
            <p><a class="talent" href="/#Talent">Talent Show</a></p>
          </li>
          <!-- <li>
            <p><a class="book" href="/#Book">Book Us</a></p>
          </li> -->
        </div>
      </ul>
    </header>
    <div class="initialScreen">
      <div class="typographybg">
        <h1 class="bigtypography unselectable">WHAT IS AFC?</h1>
      </div>
      <div class="aboutbox selectable">
        <h1 class="clubname">AUDITORIUM FACILITIES CREW</h1>
        <br>
        <h2 class="gradient-text subheading">ABOUT US</h2>
        <p class="clubinfo" style="font-size: 18px !important;">The AFC is responsible for working the more technical parts of the MCI auditorium. We help out with assemblies, extracurricular activities, and more by setting up the stage lights, speakers and other equipment. Click the button below to view more detailed information such as auditorium specifications or to book our auditorium.</p>
        <button class="applybtn moveup" @click="openBookingModal">BOOK US</button>
        <button class="applybtn" @click="openInfoModal('COMING SOON', `We're still gathering information, this may take a little longer to get online due to the CUPE Strike. We are not currently allowed into the school.`)">MORE INFO</button>
      </div>
      <div class="backgroundimg">
        <img src="./assets/board3.png" class="backgroundimg boardimgshift">
      </div>
      <div class="scrollReminder">
        <p>Scroll to see more</p>
      </div>
    </div>
    <div class="secondScreen" id="Talent">
      <div class="typographybg2">
        <h1 class="bigtypography2 unselectable">TALENT SHOW SIGNUPS</h1>
      </div>
      <div class="aboutbox2 selectable">
        <h1 class="clubname">TALENT SHOW REGISTRATION</h1>
        <br>
        <h2 class="gradient-text subheading2">December 23rd</h2>
        <p class="clubinfo">Our normal Winter Assembly has been replaced with a Talent Show! Click the button below to
          audition for an act!</p>
        <button class="applybtn" @click="openTalentModal">GOT TALENT?</button>
      </div>
      <div class="backgroundimg2">
        <img src="./assets/drumkit.png" class="backgroundimg2">
      </div>
    </div>
    <!-- <div class="thirdScreen" id="Book">
      <div class="typographybg">
        <h1 class="bigtypography unselectable">BOOK A VENUE</h1>
      </div>
      <div class="aboutbox selectable">
        <h1 class="clubname">BOOK OUR AUDITORIUM</h1>
        <br>
        <h2 class="gradient-text subheading2">LET'S RUN A SHOW!</h2>
        <p class="clubinfo">Have a show you need to run, need a great auditorium to run it in in the Etobicoke area? Book our auditorium. If you're an in school organization, you can book us for free. If not, we can negotiate a price.</p>
        <button class="applybtn moveup">AUD SPECS</button>
        <button class="applybtn" @click="openBookingModal">BOOK US</button>
      </div>
      <div class="backgroundimg2">
        <img src="./assets/aud.jpg" class="backgroundimg2">
      </div>
    </div> -->
  </div>
</template>

<script>
import BookForm from "./components/BookForm.vue"
import InfoModal from "./components/InfoModal.vue"
import TalentModal from "./components/TalentShowSignup.vue"

export default {
  name: 'App',
  components: {
    BookForm,
    InfoModal,
    TalentModal
  },
  data() {
    return {
      bookingModalOpen: false,
      infoModalData: {},
      preventScrolling: false,
      talentModalOpen: false
    }
  },
  methods: {
    openBookingModal() {
      this.bookingModalOpen = true
      this.preventScrolling = true
    },
    openTalentModal() {
      this.talentModalOpen = true
      this.preventScrolling = true
    },
    closeTalentModal() {
      this.talentModalOpen = false
      this.preventScrolling = false
    },
    closeBookingModal() {
      this.bookingModalOpen = false;
      this.preventScrolling = false
    },
    openInfoModal(header, text) {
      this.infoModalData.open = true
      this.preventScrolling = true
      this.infoModalData.header = header
      this.infoModalData.text = text
    },
    closeInfoModal() {
      this.infoModalData.open = false;
      this.preventScrolling = false
    },
  }, 
  watch: {
    preventScrolling: function() {
      if (this.preventScrolling) {
        return document.documentElement.style.overflow = 'hidden'
      } else {
        return [document.documentElement.style.overflow = 'auto', document.documentElement.style.overflowX = 'hidden']
      }
    },    
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
@media only screen and (max-device-width: 480px) {
  .backgroundimg {
    display: none;
  }
  .backgroundimg2 {
    display: none;
  }
  .header {
    display: none !important;
  }
  li {
    display: none;
  }
  .navbox {
    display: none;
  }
  ul {
    display: none;
  }
  li p {
    display: none;
  }
  .scrollReminder {
    display: none;
  }
  .typographybg {
    display: none;
  }
  .typographybg2 {
    display: none;
  }
  .aboutbox {
    backdrop-filter: none !important;
    background-color: #191919c0;
    border-radius: 2rem;
    backdrop-filter: blur(4px);
    background-color: #191919c0;
    border-radius: 2rem;
    box-shadow: 0px 0px 20px rgba(0, 0, 0, 0.5);
    left: 0 !important;
    right: 0 !important;
    line-height: 1.2;
    margin: auto !important;
    padding: 25px;
    position: absolute;
    text-align: center;
    top: 10% !important;
    width: 400px !important;
    z-index: 10;
    height: 650px !important;
  }
  .clubname {
    color: var(--mciafcorange);
    font-weight: 600;
    font-size: 50px !important;
    left: 0;
    position: absolute;
    right: 0;
    top: 10px;
  }
  .aboutbox2 {
    backdrop-filter: none !important;
    background-color: #191919c0;
    border-radius: 2rem;
    backdrop-filter: blur(4px);
    background-color: #191919c0;
    border-radius: 2rem;
    box-shadow: 0px 0px 20px rgba(0, 0, 0, 0.5);
    left: 0 !important;
    right: 0 !important;
    line-height: 1.2;
    margin: auto !important;
    padding: 25px;
    position: absolute;
    text-align: center;
    top: 10% !important;
    width: 400px !important;
    z-index: 10;
    height: 650px !important;
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
  .bookModal {
    box-shadow:.8rem .8rem 1.4rem #151515, 
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
    width: 300px !important;
    background-color: #313131 !important;
    font-size: 16px;
    z-index: 20000000;
    padding: 20px;
    padding-left: 75px;
    padding-right: 75px;
    border-radius: 3rem;
    overflow: hidden;
  }
}

@import url('https://fonts.googleapis.com/css2?family=Poppins:ital,wght@0,300;0,400;0,600;1,800&display=swap');

::-webkit-scrollbar {
    display: none;
    width: 0;
}

/* buttons.css */
button {
  animation: reverse-gradient 200ms forwards ease-out;
  background-size: 400% 400%;
  background-position: 0% 0%;
  background-image: linear-gradient(-45deg, rgb(229, 157, 22), #57acdc);
  border-radius: 0.3rem;
  border: none;
  box-shadow: 0px 0px 5px rgba(0, 0, 0, 0.596);
  color: white;
  font-family: 'Poppins', sans-serif;
  font-size: 1.2rem;
  font-weight: 400;
  max-width: fit-content;
  padding: 7px 18px;
}

button:hover {
  animation: gradient 200ms forwards ease-out;
  cursor: pointer;
}

#email {
  display: none;
}

@keyframes gradient {
  0% {
    background-position: 0% 50%;
    bottom: 60px;
  }

  100% {
    background-position: 100% 50%;
    bottom: 65px;
  }
}

@keyframes reverse-gradient {
  0% {
    background-position: 100% 50%;
    bottom: 65px;
  }

  100% {
    background-position: 0% 50%;
    bottom: 60px;
  }
}

@keyframes scrollReminderPopIn {
  0% {
    transform: translateX(120%);
  }

  100% {
    transform: translateX(0%);
  }
}

button:disabled {
  cursor: not-allowed;
}


/* contactpagestyles.css */
.contact {
  align-self: center;
  text-align: center;
}

.wrappercontactpg {
  align-items: center;
  display: flex;
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
  color: lightblue;
  font-family: 'Poppins', sans-serif;
  font-weight: 600;
  text-decoration: none;
}

a:visited {
  color: lightblue;
}

html {
  scroll-behavior: smooth;
  scrollbar-width: none; /* Firefox */
}




.unselectable {
  -moz-user-select: none;
  -ms-user-select: none;
  -webkit-touch-callout: none;
  -webkit-user-select: none;
  user-select: none;
}

.selectable {
  -moz-user-select: auto !important;
  -ms-user-select: auto !important;
  -webkit-touch-callout: auto !important;
  -webkit-user-select: auto !important;
  user-select: auto !important;
}

.header {
  background-color: var(--mciafcblack);
  box-shadow: 0px 0px 20px rgba(12, 12, 12, 0.75);
  padding-top: 30px;
  display: block;
  align-content: center;
  position: fixed;
  width: 100%;
  height: 50px;
  z-index: 100;
}

/* .about {
  position: fixed;
  left: 30px;
  top: 26.5px;
}

.talent {
  position: fixed;
  left: 130px;
  top: 26.5px;
}

.book {
  position: fixed;
  left: 230px;
  top: 26.5px;
} */

/* header.css */
li {
  float: left;
  padding-left: 16px;
  padding-right: 16px;
  padding-bottom: 10px;
}

li p {
  color: white;
  display: block;
  padding-top: 0;
  text-align: center;
  text-decoration: none;
}

ul {
  list-style-type: none;
  margin: 0;
  position: absolute;
  top: 13px;
  overflow: hidden;
  padding: 0;
}

.navbox {
  display: flexbox;
  flex-wrap: nowrap;
  margin-bottom: 10px;
  height: 60px;
  top: 0;
  left: 0;
}




/* initialscreen.css */

body {
  -ms-overflow-style: none;  /* IE and Edge */
  background-color: var(--mciafcblack);
  color: #FFF;
  font-family: 'Poppins', sans-serif;
  font-weight: 400;
  margin-left: 0;
  margin-top: 0;
  overflow-x: hidden;
  scrollbar-width: none;  /* Firefox */
  z-index: 2;
}

.initalScreen {
  position: absolute;
  margin: 0;
  top: 0;
  left: 0;
  height: 100%;
  width: 100%;
}

.secondScreen {
  position: absolute;
  margin: 0;
  top: 115%;
  left: 0;
  height: 100%;
  width: 100%;
}

.thirdScreen {
  position: absolute;
  margin: 0;
  top: 230%;
  left: 0;
  height: 100%;
  width: 100%;
}



.clubname {
  color: var(--mciafcorange);
  font-weight: 600;
  font-size: 55px;
  left: 0;
  position: absolute;
  right: 0;
  top: 10px;
}

.backgroundimg {
  height: 100%;
  margin: 0;
  overflow: hidden;
  position: absolute;
  width: 1920px;
}

.applybtn {
  bottom: 60px;
  margin: auto;
  font-weight: 600;
  left: 0;
  max-width: fit-content;
  padding-top: 7px !important;
  padding-bottom: 7px !important;
  position: absolute;
  right: 0;
  scale: 1.5;
}

.moveup {
  bottom: 140px !important;
  transition: all 200ms !important;
}

.moveup:hover {
  transform: translateY(-5px)
}

.aboutbox {
  backdrop-filter: blur(4px);
  background-color: #191919c0;
  border-radius: 2rem;backdrop-filter: blur(4px);background-color: #191919c0;border-radius: 2rem;box-shadow: 0px 0px 20px rgba(0, 0, 0, 0.5);height: 650px;left: 5%;line-height: 1.2;margin: 0;padding: 25px;position: absolute;text-align: center;top: 17.5%;width: 500px;z-index: 10;
  box-shadow: 0px 0px 20px rgba(0, 0, 0, 0.5);
  height: 650px;
  left: 5%;
  line-height: 1.2;
  margin: 0;
  padding: 25px;
  position: absolute;
  text-align: center;
  top: 17.5%;
  width: 500px;
  z-index: 10;
}

.clubinfo {
  font-size: 21.5px;
  font-weight: 200;
  left: 0;
  line-height: 1.5;
  margin: auto;
  position: absolute;
  right: 0;
  top: 36.5%;
  width: 400px;
}

.gradient-text {
  -webkit-background-clip: text;
  background-clip: text;
  background-image: linear-gradient(-45deg, rgb(229, 157, 22), #57acdc);
  background-size: 200% 200%;
  color: transparent;
}

.subheading {
  font-size: 40px;
  font-weight: 600;
  left: 0;
  margin: auto;
  position: absolute;
  right: 0;
  top: 200px;
}



/* Scroll Reminder */

.scrollReminder {
  background-position: 100% 50%;
  background-size: 200% 200%;
  background: linear-gradient(-45deg, rgb(229, 157, 22), #57acdc);
  border-radius: 0.3rem;
  bottom: 30px;
  overflow: hidden;
  padding-left: 20px;
  padding-right: 20px;
  position: absolute;
  right: 30px;
  z-index: 1;
  /* animation: scrollReminderPopIn 600ms forwards 5000ms;
  transform: translateX(120%); */
}


.bigtypography {
  -webkit-background-clip: text;
  background-clip: text;
  background-image: linear-gradient(-45deg, rgb(229, 157, 22, 0.5), #57acdc);
  background-position: 50% 200%;
  background-size: 200% 200%;
  color: transparent;
  font-family: 'Poppins', sans-serif;
  font-size: 150px;
  font-style: italic;
  font-weight: 800;
  line-height: 1;
  opacity: 0.9;
  padding-left: 50%;
  text-shadow: 10px 5px 2px rgba(255, 255, 255);
}

.typographybg {
  height: fit-content;
  margin: 0;
  position: absolute;
  right: 5%;
  text-align: right;
  top: 20%;
  z-index: 2;
  width: fit-content;
}



/* secondscreen.css */

.aboutbox2 {
  backdrop-filter: blur(4px);
  background-color: #191919c0;
  border-radius: 2rem;backdrop-filter: blur(4px);background-color: #191919c0;border-radius: 2rem;box-shadow: 0px 0px 20px rgba(0, 0, 0, 0.5);height: 650px;right: 5%;line-height: 1.2;margin: 0;padding: 25px;position: absolute;text-align: center;top: 17.5%;width: 500px;z-index: 10;
  box-shadow: 0px 0px 20px rgba(0, 0, 0, 0.5);
  height: 650px;
  line-height: 1.2;
  margin: 0;
  padding: 25px;
  position: absolute;
  right: 5%;
  text-align: center;
  width: 500px;
  z-index: 10;
}

.backgroundimg2 {
  height: 100%;
  margin: 0;
  position: absolute;
  width: 1920px;
  z-index: -1;
}

.applybtn2 {
  bottom: 50px;
  left: 33%;
  position: absolute;
}

.subheading2 {
  font-size: 40px;
  font-weight: 600;
  left: 0;
  margin: auto;
  position: absolute;
  right: 0;
  top: 200px;
}

.bigtypography2 {
  -webkit-background-clip: text;
  background-clip: text;
  background-image: linear-gradient(-45deg, rgb(229, 157, 22, 0.5), #57acdc);
  background-position: 50% 200%;
  background-size: 200% 200%;
  color: transparent;
  font-family: 'Poppins', sans-serif;
  font-size: 150px;
  font-style: italic;
  font-weight: 800;
  line-height: 1;
  opacity: 0.9;
  padding-right: 40%;
  text-shadow: 10px 5px 2px rgba(255, 255, 255);
}

.typographybg2 {
  height: fit-content;
  margin: 0;
  position: absolute;
  left: 5%;
  text-align: left;
  top: 20%;
  width: fit-content;
}
</style>
