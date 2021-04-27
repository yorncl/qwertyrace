<template>
  <div class="container" v-bind:class="{ lost: state == State.LOST, bonusTime: isBonusTime}">
    <div>
      <h1 v-bind:class="{hidden: state != State.LOST}" >{{taunt}}</h1>
      <div>
        <div id="typehere">
          <span>{{this.layout}}</span>
          <div id="input" v-bind:class="{ lineThrough: state == State.LOST }">{{this.text}}<div v-if="state != State.LOST" id="caret"> </div></div>
        </div>
      </div>
      <div>
        <div id="stats">
            <h1 v-bind:class="{hidden: state == State.LOST}">Score : {{score}}</h1>
            <h1 v-bind:class="{hidden: state != State.LOST}" >High score: {{highScore}}</h1>
            <div v-bind:class="{hidden: state != State.LOST}" id="restart" v-on:click="resetGame()">Press any key to restart</div>
        </div>
      </div>
    <life-bar :points=lifePoints> </life-bar>
    </div>
    <h2 id="toggleLayout" v-on:click="toggleLayout()">Switch to {{this.layout == this.qwerty ? this.azerty : this.qwerty}}</h2>
  </div>
</template>

<script>
import LifeBar from '../components/LifeBar.vue';

const State = Object.freeze({"WAITING":1, "TYPING":2, "LOST":3})

const startLifePoints = 15

export default {
  components: { LifeBar },


  data()
  {
    return {
      State,
      state : State.WAITING,
      text: "",
      index: 0,
      lifePoints: 0,
      qwerty: "qwerty",
      azerty: "azerty",
      layout: "",
      taunt: "",
      highScore: 0,
      score: 0,
      phrases: ["You suck", "You are pathetic", "Get a job", "Go play fortnite you basic fuck", "Type like a man", "You should consider giving up"],
      pointsInterval : null,
      bonusInterval : null,
      isBonusTime : false
    }
  },
  methods : {

    lose()
    {
      this.lifePoints = 0
      clearInterval(this.pointsInterval)
      this.state = State.LOST
      this.taunt = this.getRandomTaunt()
      if (this.highScore < this.score)
        this.highScore = this.score
      this.switchListener(this.listenKeys, this.listenAnyKey)
    },
    start()
    {
      this.state = State.TYPING
      this.switchListener(this.listenFirstKey, this.listenKeys, 0)
      this.pointsInterval = setInterval(() => {
        if (this.lifePoints > 0)
          this.lifePoints -= 1
        else
        {
          this.lose()
          clearInterval(this.pointsInterval)
        }
      }, 100)
    },
    bonusTime()
    {
      this.isBonusTime = true
      this.bonusInterval = setInterval(() => {
        if (this.lifePoints < 100)
        {
          this.isBonusTime = false
          clearInterval(this.bonusInterval)
          this.bonusInterval = null
        }
      }, 500)
    },
    resetGame()
    {
      this.state = State.WAITING
      this.resetLine()
      this.score = 0;
      this.lifePoints = startLifePoints;
      this.switchListener(this.listenAnyKey, this.listenFirstKey, 200)
    },
    toggleLayout()
    {
      if (this.state == State.LOST)
        this.resetGame()
      if (this.state == State.WAITING)
      {
        if (this.layout == this.qwerty)
          this.layout = this.azerty
        else
          this.layout = this.qwerty
      }
    },
    resetLine()
    {
      this.index = 0
      this.text = ""
    },
    getRandomTaunt()
    {
      return this.phrases[Math.floor(Math.random() * this.phrases.length)]
    },
    listenAnyKey(e)
    {
      this.resetGame()
    },
    listenFirstKey(e)
    {
      var inp = String.fromCharCode(e.keyCode);
      if (/[a-zA-Z0-9-_ ]/.test(inp))
      {
        this.start()
        this.listenKeys(e)
      }
    },
    listenKeys(e)
    {
        var inp = String.fromCharCode(e.keyCode);
        if (/[a-zA-Z0-9-_ ]/.test(inp))
        {
          this.text += e.key
          if (e.key != this.layout[this.index])
          {
            this.lose()
          }
          else
          {
            this.index++
            if (this.index == this.layout.length)
            {
              this.resetLine()
              this.score++
              this.lifePoints += 4.5
              if (this.lifePoints > 102)
              {
                if (this.isBonusTime == false)
                {
                  this.bonusTime()
                }
                this.lifePoints = 102
              }
            }
          }
        }
    },
    switchListener(l1, l2, timeout = 400)
    {
      window.removeEventListener("keyup", l1)
      if (timeout > 0)
      {
        setTimeout(() => {
          window.addEventListener("keyup", l2)
        }, timeout)
      }
      else
          window.addEventListener("keyup", l2)
    }
  },
  mounted()
  {
    setTimeout(this.addListener, 500)
    this.taunt = "Type here"
    this.layout = this.qwerty
    this.state = State.WAITING
    this.lifePoints = startLifePoints
    setTimeout(() => {
        window.addEventListener("keyup", this.listenFirstKey)
    }, 500)
 }
};
</script>

<style>
.container {
   -moz-box-shadow:    inset 0 0 100px #000000;
   -webkit-box-shadow: inset 0 0 100px #000000;
   box-shadow:         inset 0 0 100px #000000;
  background-color: #1e5cba;
  margin: 0 auto;
  height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
  transition: all .5s;
  font-family: 'Courier New', Courier, monospace;
}

.container.lost{
  background-color: #ba1e1e;
}

.container.bonusTime
{
  animation-name: bonusBlink;
	animation-duration: 0.5s;
	animation-iteration-count: infinite;
	animation-direction: alternate;
}

@keyframes bonusBlink {
  0%, 100% {
      background-color: #1e5cba;
  }
  50% {
      background-color: #4b00a2;
  }
}

.container > div {
  height: 50%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-around;
}

#typehere {
  background-color: rgba(0, 0, 0, 0.3);
  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.3);
  display: flex;
  align-items: flex-start;
  justify-content: center;
  text-align: center;
  /* width: 30vw; */
  /* height: 15vh; */
  font-family:Arial, Helvetica, sans-serif;
  color: white;
  font-size: 8em;
  font-weight: bold;
  position: relative;
}

#typehere span:first-child {
  width: 100%;
  height: 100%;
  text-align: left;
  color: rgba(0, 0, 0, 0.3);
}

@keyframes blink {
  0% {opacity: 1;}
  50% {opacity: 0;}
  100% {opacity: 1;}
}

.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to {
  opacity: 0;
}

#input {
  position: absolute;
  height: 100%;
  width: 100%;
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-items: flex-start;
}

#input.lineThrough {
  text-decoration: line-through;
}

#caret {
  position: relative;
  animation-name: blink;
  animation-duration: 1s;
  animation-iteration-count: infinite;
  display: block;
  width: 10px;
  height: 80%;
  transition: all 0.3s;
  background-color: grey;
}


h1 {
  font-size: 3em;
  color: white;
  opacity: 1;
  transition: all .4s;
}

h1.hidden
{
  opacity: 0;
}

#stats > *
{
  opacity: 1;
  transition: all .4s;
}

#stats > *.hidden
{
  opacity: 0;
}

#restart {
  position: relative;
  top: 25px;
  color: white;
  border: 6px solid white;
  padding: 15px;
  cursor: pointer;
}

#toggleLayout {
  position: absolute;
  color: white;
  bottom: 15px;
  cursor: pointer;
  transition: all 0.3s;
}

#toggleLayout:hover {
  text-shadow: white 0 0 7px;
}

</style>
