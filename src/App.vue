<template>
  <div id="app">
    <div class="wrapper">
      <button class="plate top-left-yellow" :disabled="!canClick" @click="panelClicked" @mousedown="clickOn"  id=0 :class="{active : panels.topLeft.active}"></button>
      <button class="plate top-right-red" :disabled="!canClick" @click="panelClicked" @mousedown="clickOn" id=1 :class="{active : panels.topRight.active}"></button>
      <button class="plate bottom-left-blue" :disabled="!canClick" @click="panelClicked" @mousedown="clickOn"  id=2 :class="{active : panels.bottomLeft.active}"></button>
      <button class="plate bottom-right-green" :disabled="!canClick" @click="panelClicked" @mousedown="clickOn" id=3 :class="{active : panels.bottomRight.active}"></button>

    </div>
    <div>
      <button @click="newGame()" class="btn" >Начать игру</button>
      <div class="difficulty">
        <div>Сложность</div>
        <div class="radioWrapper">
          <div>
            <label for="easy">Easy</label>
            <input type="radio" v-model="difficulty"  name="difficulty" id="easy" :value="1500">
          </div>
          <div>
            <label for="normal">Normal</label>
            <input type="radio" v-model="difficulty" name="difficulty" id="normal" :value="1000">
          </div>
          <div>
            <label for="hard">Hard</label>
            <input type="radio" v-model="difficulty" name="difficulty" id="hard" :value="400">
          </div>
        </div>
        <audio ref="sound1" src="./sounds/1.mp3"></audio>
        <audio ref="sound2" src="./sounds/2.mp3"></audio>
        <audio ref="sound3" src="./sounds/3.mp3"></audio>
        <audio ref="sound4" src="./sounds/4.mp3"></audio>
      </div>
    </div>

  </div>
</template>

<script>


export default {
  name: 'App',
  data(){
    return{
      difficulty: 400,
      canClick:false,
      panels:{
        topLeft:{active:false},
        topRight:{active:false},
        bottomLeft:{active:false},
        bottomRight:{active:false},

      },

      sequense:[],
      sequenseToGuess: []
    }
  },


  methods:{
    log(){
      console.log(Object.values(this.$refs))
    },
    async flash(number){

      let panel = Object.values(this.panels)[number]
      return new Promise((resolve,reject)=>{
        setTimeout(()=>{
          panel.active = true
          this.soundPlay(number)
          setTimeout(()=>{
            panel.active = false
            resolve()
          },100)

        },this.difficulty)
      })
    },
    newGame(){
      this.sequense = this.sequenseToGuess = []
      this.sequense.push(this.getRandomColor())
      this.main()

    },
    clickOn(e){
      this.soundPlay(e.target.id)
    },
    soundPlay(number){

      for(let sound of Object.values(this.$refs)){
        sound.pause()
        sound.currentTime = 0
      }
      Object.values(this.$refs)[number].play()
    },
    async main(){
      this.setSequense()
      setTimeout(async()=>{
        for(let number of this.sequense){
        await this.flash(number)
      }
      this.canClick = true
      },500)

    },
    getRandomColor(){
      let numbers = [0,1,2,3]
      return numbers[parseInt(Math.random()*numbers.length)]
    },
    panelClicked(e){

      if(!this.canClick){
        return
      }
      if(+e.target.id === this.sequenseToGuess[0]){
        this.sequenseToGuess.shift()
      }else{
        this.canClick = false
        window.alert('GAME OVER')
      }
      if(this.sequenseToGuess.length === 0){
        this.sequense.push(this.getRandomColor())
        this.main()
      }

    },
    setSequense(){
      this.sequenseToGuess = [...this.sequense]
    }

  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
*{
  box-sizing: border-box;
}
body{
  background: #2C3E50;
}
.wrapper{
  position: relative;
  margin: auto auto;
  height: 410px;
  width: 410px;
  display: flex;
  flex-wrap: wrap;
  justify-content:space-around;
}
.wrapper::after{
  position: absolute;
  content: ' ';
  display: block;
  height: 200px;
  width: 200px;
  background: #2C3E50;
  border-radius: 100%;
  transform: translate(-50%,-50%);
  top:50%;
  left: 50%;
}
.plate{
  height: 200px;
  width: 200px;
  cursor: pointer;
  border: none;
}

.plate:active{
  border: 10px solid white;
}

.top-left-yellow{
  border-top-left-radius: 100%;
  background: #EAB543;
}
.top-right-red{
  border-top-right-radius: 100%;
  background: #FC427B;
}
.bottom-left-blue{
  border-bottom-left-radius: 100%;
  background: #1B9CFC;
}
.bottom-right-green{
  border-bottom-right-radius: 100%;
  background: #55E6C1;
}.active{
  background: white;
}
.difficulty{
  margin-top: 20px;
  font-size: 1.2rem;
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}
.difficulty .radioWrapper{
  margin-top: 20px;
  width: 250px;
  display: flex;
  justify-content: space-between;
}
.btn{
  color: white;
  font-size: 1.2rem;
  margin-top: 20px;
  border: none;
  padding: 10px;
  border-radius: 10px;
  background-color: #25CCF7;
}
</style>
