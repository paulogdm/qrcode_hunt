<template>
<div>
  <div v-if="toggle">
    <Barcode v-on:toggle="onToggle" @result="gotResult"/>
  </div>
  <div class="todo" v-else>
    <div>
      <h1>Quests</h1>  
      <ul class="instru"> <li>To read the secret code, press the "Investigate" button.</li></ul>

      <div class="quests" v-if="quests[0]">
        <ol>
          <li v-for="(quest, index) in quests" :key="index" class="quest" :class="{'is-completed': quest.completed}">{{quest.title}}</li>
        </ol>
        <div class="clue">
          <span>Clue:</span>
          <p>{{quests[state].clue}}</p>
        </div>
        <button class="inv btn btn-lg" type="button" v-on:click="onToggle">Investigate</button>
      </div>
      <div v-else>
        <div class="no-game">{{game}}</div>
      </div>
    </div>
    
  </div>
  </div>
</template>

<script>
import Barcode from '../components/Barcode.vue'
import axios from 'axios'

export default {
  components: {
    Barcode
  },
  name: "Quest",
  data() {
    return {
      toggle: false,
      game: "loading...",
      quests: [],
      state: 0
    }
  },
  methods: {
    onToggle () {
      this.toggle = !this.toggle;
    },
    checkGameState () {
      if (this.state++ == this.quests.length) {
        alert("That's it! You are soooo Clutch! (hi, daaaeee! 😘😘😘)");
        this.state--;
      }
    },
    gotResult (result) {
      let completed = this.quests.map(quest => quest.completed = quest.message === result ? !quest.completed : quest.completed);
      /*  
        @TODO
        try filter
      */
      this.onToggle();
      this.checkGameState();
    }
  },
  created() {
    axios.get('/game?event=test')
      .then(res => {
        if (res.data.quests[0])
          this.quests = res.data.quests;
        else
          this.game = "Looks like there aren't any games at the moment. 😔";
      })
      .catch(this.game = "#err")
  }
}
</script>

<style scoped>
.no-game {
  margin: 100px 50px;
  text-align: center;
  font-size: 24px;
}
.todo {
  margin: 10px auto;
  padding: 5px 20px;
  border-radius: 2px;
  /* padding-bottom: 50px; */
  background-color: #F7F7F722;  
  max-width: 480px;

}
.quest {
  padding: 10px 10px;
}
.instru {
  padding-left: 10px;
  opacity: .4;
}
.is-completed::after {
  content: " ✔";
  color: rgb(0, 255, 0);
}
.quests button {
    width: 100%;
    display: block;
    margin-bottom: 10px;
    z-index: 1;
    position: relative;
    -moz-box-sizing: border-box;
    -webkit-box-sizing: border-box;
    box-sizing: border-box;
}


.clue {
  margin: 50px 0;
}

/* .inv { */
  /* position: absolute; */
  
  /* bottom: 15px; */
/* } */
</style>
