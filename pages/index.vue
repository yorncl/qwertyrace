<template>
  <div class="container" v-bind:class="{ lost: !isPlaying }">
    <div>
      <h1 v-bind:class="{hidden: isPlaying}" >{{taunt}}</h1>
      <div>
        <div id="typehere">
          <span>qwerty</span>
          <div id="input" v-bind:class="{ lineThrough: !isPlaying }">{{this.text}}<div v-if="isPlaying" id="caret"> </div></div>
        </div>
      </div>
      <div>
        <div id="stats">
            <h1 v-bind:class="{hidden: !isPlaying}">Score : {{score}}</h1>
            <h1 v-bind:class="{hidden: isPlaying}" >High score: {{highScore}}</h1>
            <div v-bind:class="{hidden: isPlaying}" id="restart" v-on:click="resetGame()">Press any key to restart</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {

  data()
  {
    return {
      text: "",
      index: 0,
      qwerty: "qwerty",
      taunt: "",
      isPlaying: true,
      highScore: 0,
      score: 0,
      phrases: ["You suck", "You are pathetic", "Get a job", "Go play fortnite you basic fuck", "Type like a man", "You should consider giving up"]
    }
  },
  methods : {

    lost()
    {
      this.taunt = this.getRandomTaunt()
      if (this.highScore < this.score)
        this.highScore = this.score
      this.isPlaying = false
      window.removeEventListener("keyup", this.listenKeys)
      setTimeout(() => {
        window.addEventListener("keyup", this.listenAnyKey)
      }, 600)
    },
    resetGame()
    {
      this.isPlaying = true
      this.resetLine()
      this.addListener()
      this.score = 0;
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
    listenKeys(e)
    {
      console.log("ok")
        var inp = String.fromCharCode(e.keyCode);
        if (/[a-zA-Z0-9-_ ]/.test(inp))
        {
          this.text += e.key
          if (e.key != this.qwerty[this.index])
          {
            this.isPlaying = false
            this.lost()
          }
          else
          {
            this.index++
            if (this.index == this.qwerty.length)
            {
              this.resetLine()
              this.score++
            }
          }
        }
    },
    addListener()
    {
      window.removeEventListener("keyup", this.listenAnyKey)
      setTimeout(() => {
        window.addEventListener("keyup", this.listenKeys)
      }, 300)

    }
  },
  mounted()
  {
    setTimeout(this.addListener, 500)
    this.taunt = "Type here"
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

</style>
