<template>
    <div class="game">
            <div class="isntructions">
        <h2>Instructions</h2>
<input type="radio" id="easy" value="easy" @click ="changeMode('easy')" v-model="picked">
<label for="one">Easy</label>
<br>
<input type="radio" id="normal" value="normal" @click ="changeMode('normal')" v-model="picked">
<label for="two">Normal</label>
<br> 
<input type="radio" id="hard" value="hard" @click ="changeMode('hard')" v-model="picked">
<label for="two">Hard</label>
<br>   
<div class="mode">Your mode: {{picked}}</div>
<div class="start-button" v-on:click="gameStart()" :class="{'disabled' : isPlaying}">{{state}}</div> 
</div>
      <div class="game-box">
    <div class="row">
      <div class="box box1" v-on:click="clickedBox(1)" :class="{ active: isBoxOneActive}">
      </div>
      <div class="box box2" v-on:click="clickedBox(2)" v-bind:class="{ active: isBoxTwoActive}">
      </div>
    </div>
    <div class="row">
      <div class="box box3" v-on:click="clickedBox(3)" v-bind:class="{ active: isBoxThreeActive}">
      </div>
      <div class="box box4" v-on:click="clickedBox(4)" v-bind:class="{ active: isBoxFourActive}">
      </div>
    </div>
          <div class="score">Your score: {{score}}</div>
          <div class="message">{{message}}</div>
    </div>
  </div>
</template>

<script>
let timer;
export default {
  name: 'SimonGame',
  timer,
  data(){
    return {picked: '',
    isEasyModeOn: true,
    isNormalModeOn: false,
    isHardModeOn: false,
    state: 'Start',
    isPlaying: false,
    playerTurn: false,
    pattern: [],
    index: 0,
    isBoxOneActive: false,
    isBoxTwoActive: false,
    isBoxThreeActive: false,
    isBoxFourActive: false,
    score: 0,
    message: null,
    audio:['https://s3.amazonaws.com/freecodecamp/simonSound1.mp3',
    'https://s3.amazonaws.com/freecodecamp/simonSound2.mp3',
    'https://s3.amazonaws.com/freecodecamp/simonSound3.mp3',
    'https://s3.amazonaws.com/freecodecamp/simonSound4.mp3',
    'http://soundbible.com/mp3/Sad_Trombone-Joe_Lamb-665429450.mp3'
    ]
    }
  },

  methods: {
    //starting or ending the game
    gameStart() {
      if (this.state === 'Start') {
        this.state = 'Stop';
        this.message = null;
        this.isPlaying = true;
        this.computerTurn();
      } else {
        this.resetGame();
      }
    },

    //counting steps of player and showing patterns
    computerTurn() {
      this.index = 0;
      this.pattern.push(this.getRandomNumberOneToFour());
      this.showPattern();
    },

    isPlayerTurnOnOff(){
      this.playerTurn = !this.playerTurn;
    },

    //Utility method to get a random number from 1 to 4
    getRandomNumberOneToFour() {
      return Math.floor(Math.random() * 4 + 1);
    },

    //changing Mode
    changeMode(mode){
      if(this.state !== 'Start'){
        return;
      }
      if(mode ==='easy'){
        this.isEasyModeOn = true;
        this.isNormalModeOn = false;
        this.isHardModeOn = false;
      } else if(mode === 'normal'){
        this.isEasyModeOn = false;
        this.isNormalModeOn = true;
        this.isHardModeOn = false;      
        }
        else if(mode === 'hard'){
        this.isEasyModeOn = false;
        this.isNormalModeOn = false;
        this.isHardModeOn = true;      
        }
    },

    //showing patterns
    showPattern() {
      let i=0;
      let interval;
      if(this.isEasyModeOn===true) interval = 1500;
       else if(this.isNormalModeOn ===true) interval = 1000;
       else if(this.isHardModeOn ===true) interval = 400;
      timer = setInterval(()=> {
        if(i>=this.pattern.length){
          this.stopInterval();
        }
        this.clickEffect(this.pattern[i]);
        i++;
      }, interval);
    },

    stopInterval(){
      this.isPlayerTurnOnOff();
      clearInterval(timer);
    },

    //creating click effects and checking 
    clickedBox(boxNum) {
      if (this.state === 'Start' || this.playerTurn===false) {
        return;
      }
      this.clickEffect(boxNum);
      let isCorrect = this.checkPattern(boxNum);
      if (!isCorrect) { // If user clicks wrong box
       // User playing in strict mode
          this.processGameOver();
          return;
      } else { // If the current click is correct
        if (this.index === this.pattern.length - 1) { // If total items in pattern clicked
          this.score++;
          setTimeout(()=> {
            this.computerTurn();
          }, 1000);
            this.isPlayerTurnOnOff();

        } else { // Pending clicks exist
          this.index++;
        }
      }
    },


    processGameOver(){   
      //stops the game and plays fail audio  
      let sound = new Audio(this.audio[4]);
      sound.play();
      this.message = `Game Over. You loose at ${this.score}`;
      this.resetGame();
    },

    //checking right pattern
    checkPattern(boxNum) {
      return (this.pattern[this.index] === boxNum);
    },

    clickEffect(boxNum) {
      let sound;
      //This method takes in a box number as parameter
      //Then toggles its class as active
      //Then plays the respective audio file
      //Then reverts the class back to original
      switch (boxNum) {
        case 1:
          sound = new Audio(this.audio[0]);
          this.isBoxOneActive = true;
          sound.play();
          setTimeout(()=> {
            this.isBoxOneActive = false;
          }, 100);
          break;
        case 2:
          sound = new Audio(this.audio[1]);         
          this.isBoxTwoActive = true;
          sound.play();
          setTimeout(()=> {
            this.isBoxTwoActive = false;
          }, 100);
          break;
        case 3:
          sound = new Audio(this.audio[2]);
          this.isBoxThreeActive = true;
          sound.play();
          setTimeout(()=> {
            this.isBoxThreeActive = false;
          },100);
          break;
        case 4:
          sound = new Audio(this.audio[3]);
          this.isBoxFourActive = true;
          sound.play();
          setTimeout(()=> {
            this.isBoxFourActive = false;
          }, 100);
          break;
      }
      return;
    },


  resetGame() {
      this.state = 'Start';
      this.score = 0;
      this.isPlaying = false;
      this.pattern = [];
      this.index = 0;
      this.picked = '';
      this.playerTurn = false;
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang ='scss'>
.wrapper {
}
.game {
  line-height: 2.5;
  padding: 50px 0;
    width: 70%;
 margin: 0 auto;
text-align: center;
position: relative;
display: grid;
grid-template-columns: 1fr 1fr;
align-items: center;
@media(max-width: 515px){
  display: inline;
  
}
}
.game-box {
  padding: 20px 0;
}

.box {
  display: inline-block;
  width: 150px;
  height: 150px;
  border: 5px solid #2c3e50;
  margin-left: -5px;
  margin-bottom: -20px;
  cursor: pointer;
  @media(max-width:890px){
    width: 100px;
    height: 100px;
  };
  @media(min-width:1176px){
    width: 200px;
    height: 200px;
  }
  @media(max-width: 600px){
    width: 70px;
    height: 70px;
  }
  &.active{
  background-color: #2c3e50;
  }
}

.box1 {
  background-color: #56cdff;
  border-radius: 100% 0 0 0;
}

.box2 {

  background-color: #ff8eb3;
  border-radius: 0 100% 0 0;
}

.box3 {

  background-color: #fff88e;
  border-radius: 0 0 0 100%;
}

.box4 {

  background-color: #9bff8e;
  border-radius: 0 0 100% 0;
}

.start-button{
  width: 30%;
  margin: 0 auto;
  border-radius: 7px;
            transition: 0.3s all ease;
            background-color: #42b983;
            padding: 10px 15px 5px;
            color: #fff;
            -webkit-box-shadow: 0px 5px 1px 0px rgba(19,73,49,1);
-moz-box-shadow: 0px 5px 1px 0px rgba(19,73,49,1);
box-shadow: 0px 5px 1px 0px rgba(19,73,49,1);
&:hover{
            background-color: rgba(19,73,49,1);
        }
&.disabled{
  background-color: red;
  -webkit-box-shadow: 0px 5px 1px 0px red;
-moz-box-shadow: 0px 5px 1px 0px red;
box-shadow: 0px 5px 1px 0px red;
}

}

.message{
  color: red;
}


</style>
