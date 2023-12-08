<template>
  <audio id="clip1">
  	<source src="https://s3.amazonaws.com/freecodecamp/simonSound1.mp3"/>
  </audio>
  <audio id="clip2">
  	<source src="https://s3.amazonaws.com/freecodecamp/simonSound2.mp3"/>
  </audio>
  <audio id="clip3">
  	<source src="https://s3.amazonaws.com/freecodecamp/simonSound3.mp3"/>
  </audio>
  <audio id="clip4">
  	<source src="https://s3.amazonaws.com/freecodecamp/simonSound4.mp3"/>
  </audio>
  <div class="outer-circle">
    <button class="topleft" :style="{'background': topLeft}" @click='handleClickField(1)'></button>
    <button class="topright" :style="{'background': topRight}" @click='handleClickField(2)'></button>
    <button class="bottomleft" :style="{'background': bottomLeft}" @click='handleClickField(3)'></button>
    <button class="bottomright" :style="{'background': bottomRight}" @click='handleClickField(4)'></button>
    <div class="inner-circle">
      <div class="text-[40px]">SIMON!</div>
      <div class="flex flex-row gap-6">
        <div class="flex flex-col items-center justify-between">
        <input type="checkbox" v-model="isPowerOn" @change="powerOn">
        <p>POWER</p>
      </div>
      <button class="button" @click="startGame">Start</button>
      <div>
        <div class="turn">{{ turnCounter }}</div>
          <p>
            COUNT
          </p>
        </div>
      </div>
      <select v-model="gameMode">
        <option value="" disabled selected>Select game mode</option>
        <option value="1500">Easy</option>
        <option value="1000">Middle</option>
        <option value="400">Hard</option>
      </select>
    </div>
  </div>
</template>

<script lang="ts" setup>
const order = ref([] as number[]);
const playerOrder = ref([] as number[]);
const flash = ref();
const turn = ref();
const good = ref(false);
const compTurn = ref(false);
const intervalId = ref();
const noise = ref(true);
const isPowerOn = ref(false);
const isGameWon = ref();
const topLeft = ref("darkgreen")
const topRight = ref("darkred")
const bottomLeft = ref("goldenrod")
const bottomRight= ref("darkblue")
const turnCounter = ref('')
const gameMode = ref(1000)

const powerOn = () => {
  if (isPowerOn.value == true) {
    turnCounter.value = "-";
  } else {
    turnCounter.value = "";
    clearColor();
    clearInterval(intervalId.value);
  }
}

const startGame = () => {
  if (isPowerOn.value || isGameWon.value) play();
}

const play = () => {
  isGameWon.value = false;
  order.value = [];
  playerOrder.value = [];
  flash.value = 0;
  intervalId.value = 0;
  turn.value = 1;
  turnCounter.value = '1';
  good.value = true;
  for (let i = 0; i < 20; i++) {
    order.value.push(Math.floor(Math.random() * 4) + 1);
  }
  compTurn.value = true;

  intervalId.value = setInterval(gameTurn, gameMode.value);
}

const gameTurn = () => {
  isPowerOn.value = false;

  if (flash.value == turn.value) {
    clearInterval(intervalId.value);
    compTurn.value = false;
    clearColor();
    isPowerOn.value = true;
  }

  if (compTurn.value) {
    clearColor();
    setTimeout(() => {
     universalChoiceFunction(order.value[flash.value])
      flash.value++;
    }, 200);
  }
}

const universalChoiceFunction = (field:number) => {
  
  if (noise.value) {
    const audio = <HTMLAudioElement> document.getElementById(`clip${field}`);
    audio?.play();
  }
  noise.value = true;
  switch(field){
    case 1:
    topLeft.value = "lightgreen"
    break
    case 2:
    topRight.value = "tomato"
    break
    case 3:
    bottomLeft.value = "yellow"
    break
    case 4:
    bottomRight.value = "lightskyblue"
    break
  }
  ;
}

const clearColor = () => {
  topLeft.value = "darkgreen";
  topRight.value = "darkred";
  bottomLeft.value = "goldenrod";
  bottomRight.value = "darkblue";
}

const flashColor = () => {
  topLeft.value = "lightgreen";
  topRight.value = "tomato";
  bottomLeft.value= "yellow";
  bottomRight.value = "lightskyblue";
}

const handleClickField = (field:number) => {
  if (isPowerOn.value) {
    playerOrder.value.push(field);
    check();
    universalChoiceFunction(field)
    if(!isGameWon.value) {
      setTimeout(() => {
        clearColor();
      }, 300);
    }
  }
}

const check = () => {
  if (playerOrder.value[playerOrder.value.length - 1] !== order.value[playerOrder.value.length - 1])
    good.value = false;

  if (playerOrder.value.length == 5 && good.value) {
    winGame();
  }

  if (good.value == false) {
    flashColor();
    turnCounter.value = "NO!";
    setTimeout(() => {
      turnCounter.value = turn.value;
      clearColor();
        play();

    }, gameMode.value);

    noise.value = false;
  }

  if (turn.value == playerOrder.value.length && good.value && !isGameWon.value) {
    turn.value++;
    playerOrder.value = [];
    compTurn.value = true;
    flash.value = 0;
    turnCounter.value = turn.value;
    intervalId.value = setInterval(gameTurn, gameMode.value);
  }

}

const winGame = () => {
  flashColor();
  turnCounter.value = "WIN!";
  isPowerOn.value = false;
  isGameWon.value = true;
}
</script>

<style lang="css" scoped>
.button {
  border-radius: 50%;
  font-size: 1.5em;
  width:2.5em;
  background-color: lightgray;
}

.turn {
  background: #330000;
  color: red;
  font-family: courier;
  font-size: 20px;
  height: 30px;
  width: 50px;
  text-align: center;
  vertical-align: middle;
  line-height: 30px;
}

.outer-circle {
  background: #385a94;
  border-radius: 50%;
  height: 600px;
  width: 600px;
  position: relative;
  border-style: solid;
  border-width: 10px;
  margin: auto;
  margin-top: 60px;
  box-shadow: 8px 8px 15px 5px #888888;
}

.inner-circle {
  position: absolute;
  display:flex;
  gap:25px;
  flex-direction: column;
  align-items: center;
  background: grey;
  border-radius: 50%;
  height: 300px;
  width: 300px;
  border-style: solid;
  border-width: 10px;
  top: 50%;
  left: 50%;
  margin: -150px 0px 0px -150px;
  padding:30px;
  box-sizing: border-box;
}

.topleft {
  position: absolute;
  height: 300px;
  width: 300px;
  border-radius: 300px 0 0 0;
  -moz-border-radius: 300px 0 0 0;
  -webkit-border-radius: 300px 0 0 0;
  background: darkgreen;
  top: 50%;
  left: 50%;
  margin: -300px 0px 0px -300px;
  border-style: solid;
  border-width: 5px;
  box-sizing: border-box;
  -moz-box-sizing: border-box;
  -webkit-box-sizing: border-box;
}

.topright {
  position: absolute;
  height: 300px;
  width: 300px;
  border-radius: 0 300px 0 0;
  -moz-border-radius: 0 300px 0 0;
  -webkit-border-radius: 0 300px 0 0;
  background: darkred;
  top: 50%;
  left: 50%;
  margin: -300px 0px 0px 0px;
  border-style: solid;
  border-width: 5px;
  box-sizing: border-box;
  -moz-box-sizing: border-box;
  -webkit-box-sizing: border-box;
}

.bottomleft {
  position: absolute;
  height: 300px;
  width: 300px;
  border-radius: 0 0 0 300px;
  -moz-border-radius: 0 0 0 300px;
  -webkit-border-radius: 0 0 0 300px;
  background: goldenrod;
  top: 50%;
  left: 50%;
  margin: 0px -300px 0px -300px;
  border-style: solid;
  border-width: 5px;
  box-sizing: border-box;
  -moz-box-sizing: border-box;
  -webkit-box-sizing: border-box;
}

.bottomright {
  position: absolute;
  height: 300px;
  width: 300px;
  border-radius: 0 0 300px 0;
  -moz-border-radius: 0 0 300px 0;
  -webkit-border-radius: 0 0 300px 0;
  background: darkblue;
  top: 50%;
  left: 50%;
  margin: 0px 0px -300px 0px;
  border-style: solid;
  border-width: 5px;
  box-sizing: border-box;
  -moz-box-sizing: border-box;
  -webkit-box-sizing: border-box;
}



</style>
