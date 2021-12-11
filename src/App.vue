<template>
  <div class="simon">
    <div class="buttons">
      <button @click="pressButton($event)" class="simon-button simon-1" id="0"></button>
      <button @click="pressButton($event)" class="simon-button simon-2" id="1"></button>
      <button @click="pressButton($event)" class="simon-button simon-3" id="2"></button>
      <button @click="pressButton($event)" class="simon-button simon-4" id="3"></button>
      <div class="display">
        <p>
          <span class="score" id="score">{{ round }} / {{ histoty }}</span>
          <br>
          <button @click="pressReset" class="strict">
            RESET
          </button>
          <button @click="startGame" class="strict start">
            START
          </button>
          <br>
          <button @click="level < 5 ? level++ : level" class="strict level">
            SPEED {{ level }}
          </button>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
import { Howl } from 'howler';

export default {
  name: 'App',
  data() {
    return {
      audio: [
        new Howl({src: ['https://s3.amazonaws.com/freecodecamp/simonSound1.mp3'], html5: true, autoplay: false, preload: true}),
        new Howl({src: ['https://s3.amazonaws.com/freecodecamp/simonSound2.mp3'], html5: true, autoplay: false, preload: true}),
        new Howl({src: ['https://s3.amazonaws.com/freecodecamp/simonSound3.mp3'], html5: true, autoplay: false, preload: true}),
        new Howl({src: ['https://s3.amazonaws.com/freecodecamp/simonSound4.mp3'], html5: true, autoplay: false, preload: true})      
      ],
      level: 1,
      round: 0,
      brain: [],
      player: [],
      demo: true,
      press: '',
      histoty: 0,
    }
  },
  methods: {
    pressButton(event) {
      this.audio[Number(event.currentTarget.id)].play();
      if (!this.demo) {
        event.target.classList.toggle('press');
        setTimeout(() => {
          event.target.classList.toggle('press');
        }, 500);
        this.player.push(Number(event.currentTarget.id));
        if (this.player.length === this.brain.length) {
          const temp = this.brain;
          const is_same = this.player.length == temp.length && this.player.every(function(element, index) {
              return element === temp[index]; 
          }); 
          if (is_same) {
            setTimeout(() => {
              this.round++;
            }, 1000);
            this.player.splice(0, this.player.length);
            this.brain.push(Math.floor(Math.random() * 4));
            this.demo = true;
            this.demoPlay();  
          } else {
            this.pressReset();
          }
        }
      } else {
        event.target.classList.toggle('press');
        setTimeout(() => {
          event.target.classList.toggle('press');
        }, 500 - this.level * 50);
      }
    },
    demoPlay() {
      for(let i = 0; i < this.brain.length; i++) {
        setTimeout(() => {
          document.getElementById(String(this.brain[i])).click();
          if (this.brain.length - 1 === i) {
            this.demo = false;
          }
        }, (i + 1) * (1200 - this.level * 120));
      }
    },
    startGame() {
      this.pressReset();
      this.brain.push(Math.floor(Math.random() * 4));
      this.round = 1;
      this.demoPlay();
    },
    pressReset() {
      if (this.round > this.histoty) {
        this.histoty = this.round;
        this.round = 0;
      } else this.round = 0;
      this.player.splice(0, this.player.length);
      this.brain.splice(0, this.brain.length);
      this.demo = true;
      this.level = 1;
    },
  },
};
</script>

<style lang="scss">

</style>