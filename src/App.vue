<template>
  <div id="app">
    <div class="container">
        <div class="row">
  
          <div class="col-sm" style="padding-bottom: 40px;">
            VUE JS VIDEO POKER
          </div>
          
        </div>
      </div>
    <div class="container">
      <div class="row">

        <div class="col-sm" style="max-width: 300px;">
            <table class="minimalistBlack">
            <tr><th colspan="2">PAYOUT GRID:</th></tr>
            <tr><td>Royal Flush:</td><td>500:1</td></tr>
            <tr><td>Strait Flush:</td><td>200:1</td></tr>
            <tr><td>4 Of A Kind::</td><td>100:1</td></tr>
            <tr><td>Full House:</td><td>50:1</td></tr>
            <tr><td>Srait:</td><td>20:1</td></tr>
            <tr><td>Flush:</td><td>10:1</td></tr>
            <tr><td>3 Of A Kind:</td><td>5:1</td></tr>
            <tr><td>2 Pair:</td><td>2:1</td></tr>
            <tr><td>Pair (Jacks or Better):</td><td>1:1</td></tr>
          </table>
         
        </div>

        <div class="col-sm">
          
          <!--- cards -->
            <div class="container">
              <div class="row">
                <div v-for="(card, index) in hand" :key="index" class="col-sm">
                  <!-- <img v-bind:src="imgPath+card"> -->
                  <span @click="discard(index)" class="cardBut" :style="{'pointer-events':card.disabled?'none':'auto'}"><img v-bind:src="require('@/assets/images/'+card.name)"></span>
                </div>
              </div>
              <div class="row">
                  <div class="col-sm-12" style="padding-top: 40px;">
                      <b-button v-if="gameOn" @click="discardCards()">Continue</b-button>
                      <div v-if="betSet"><b-button @click="bet(5)">Bet 5</b-button> | <b-button @click="bet(15)">Bet 15</b-button> | <b-button @click="bet(25)">Bet 25</b-button> </div>
                  </div>
              </div>
            </div>
         <!--- end cards -->

        </div>

      </div>
    </div>
  </div>
</template>

<script>


export default {
  name: 'app',
  data(){
    return{
      hand:[],
      gameOn: false,
      betSet: true,
      betValue: 0,
      changeCards: false,
      money: 50,
      } 
    },
    created(){
        this.buildEmptyHand()
    },
    methods:{
      buildEmptyHand(){
        for(let i=0; i<5; i++){
          this.hand.push({name:'back.png', face:0, suit:0, discarded: false, disabled: true})
        }
      },
      bet(value){
        this.betSet = !this.betSet
        this.betValue = value
        this.deal()
      },
      deal(){
        //game status
        this.gameOn = !this.gameOn
        let handcomplete = 0
        let validCard = true
        let currentCard = []
        // populate hand
        while(handcomplete < 5){
          validCard = true
          currentCard = this.drawCard()
          this.hand.forEach(card => {
            if(currentCard[0] == card.suit && currentCard[1] == card.value){
              validCard = false
              }
          });
          if(validCard){
            this.hand[handcomplete].suit= currentCard[0]
            this.hand[handcomplete].face= currentCard[1]
            this.hand[handcomplete].name = (currentCard[0]*100) + currentCard[1] + '.png'
            this.hand[handcomplete].disabled = false
            handcomplete++
          }
        }
        //discard cards 
        this.changeCards = !this.changeCards
      },
      drawCard(){
        let suit = _.random(1,4);
        let face = _.random(1,13);
        let cardNow = [suit, face] 
        return cardNow
      },
      discard(index){
        if(this.hand[index].discarded == false){
          this.hand[index].name = 'back.png'
          this.hand[index].discarded = true
        }else{
          this.hand[index].name = (this.hand[index].suit * 100) + this.hand[index].face +'.png'
          this.hand[index].discarded = false
        }

      },
    }
  }
</script>


