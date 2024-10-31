<template>
  <h1>War games </h1>
  <div id="game" v-if="!gameOver">
  <div class="cards">
    <div id="playerOne">
        <h3>Player One</h3>
        <div class="card"><img :src="cardOne?.images?.png" alt=""></div>
  </div>
  <div id="playerTwo">
    <h3>Player Two</h3>
        <div class="card"><img :src="cardTwo?.images?.png" alt=""></div>
  </div>
  </div>
  
     <div class="scoreboard">
      <button @click="getCards()" id="draw">draw</button>
     <h4>Player One Score : {{ playerOnescore }} &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Player Two Score : {{ playerTwoscore }}</h4>
     </div>
</div>
  

     <div v-if="gameOver" class="start">
      <button @click="getDeck()" class="over">Play Game</button>
     </div>
</template>

<script>
import { ref } from 'vue'
import axios from 'axios'

function translate (value){
    switch (value) {
      case 'JACK':
        value = '11'
        break;

        case 'QUEEN':
        value = '12'
        break;

        case 'KING':
        value = '13'
        break;

        case 'ACE':
        value = '14'
        break;
    
      default:
        break;
    }
    return value;
}

export default{
     setup(){
      const gameOver = ref(true) ,
            cardOne =ref({}) ,cardTwo =ref({}) ,
            deckId = ref(null) ,
            playerOnescore = ref(0) ,playerTwoscore = ref(0)


          async function getDeck(){
            gameOver.value = false
              if (deckId.value == null) {
                  
                const {data} = await axios.get('https://deckofcardsapi.com/api/deck/new/shuffle/?deck_count=1')
                deckId.value = data.deck_id
                console.log(data)
          }                      
        }
          async function getCards(){
            const {data} = await axios.get('https://deckofcardsapi.com/api/deck/' + deckId.value + '/draw/?count=2')
            
            const remaining = data.remaining
            console.log(remaining)
            const {cards} = data
            cardOne.value = cards[0];
            cardTwo.value = cards[1]; 

            const valueOne = parseInt(translate(cardOne.value.value))
            const valueTwo = parseInt(translate(cardTwo.value.value))

            if(valueOne > valueTwo) playerOnescore.value += 1
            if(valueOne < valueTwo)  playerTwoscore.value += 1

            if(remaining === 0 ) {
              gameOver.value = true
              playerOnescore.value = 0
              playerTwoscore.value = 0
            }
         

          }
        return {getCards ,cardOne ,cardTwo ,playerOnescore,playerTwoscore ,getDeck,gameOver}
     }

}
</script>



<style scoped>

h1{
  text-align: center;
  color: red;
}
.cards{
  margin: 0 auto;
   display: flex;
   justify-content: space-around;
   margin-bottom: 50px;
}
#draw{
  border-radius: 5px;
  background-color: greenyellow;
  color: red;
  border: none;
  width: 10%;
  height: 30px;
  margin-left: 100px;
  font-size: larger;
  font-weight: bold;
}
.scoreboard{
  margin-left: 40%;
}


.start {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh; 
}
.over{
 
  border-radius: 5px;
  background-color: greenyellow;
  color: red;
  border: none;
  width: 10%;
  height: 30px;
  font-size: larger;
  font-weight: bold;
}
</style>
