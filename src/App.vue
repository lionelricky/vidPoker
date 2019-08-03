<template>
  <div id="app">
    <div class="container">
        <div class="row">

          <div class="col-sm header" style="padding-bottom: 10px;">
            VUE JS VIDEO POKER
            <hr size="1">
          </div>

        </div>
      </div>
    <div class="container">
      <div class="row">

        <div class="col-sm" style="max-width: 300px;">
          <div class="money">Money: {{this.money}} $</div>
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
         <div v-if="win" class="winlose" style="color:red;"><strong>Winner! {{this.winner.name}}</strong></div>
         <div v-if="noWin" class="winlose">You lost. Try again.</div>
        </div>

        <div class="col-sm">

          <!--- cards -->
            <div class="container" style="margin-top: 30px;">
              <div class="row">
                <div v-for="(card, index) in hand" :key="index" class="col-sm">
                  <span @click="discard(index)" class="cardBut" :style="{'pointer-events':card.disabled?'none':'auto'}"><img v-bind:src="require('@/assets/images/'+card.name)"></span>
                </div>
              </div>
              <div class="row">
                  <div class="col-sm-12" style="padding-top: 40px;" v-if="!noMoney">
                      <div v-if="changeCards" >
                        <span class="message"> Select cards to discard and click continue.</span><br/><br/>
                        <b-button @click="deal()">Continue</b-button>
                      </div>
                      <div v-if="!gameOn">
                        <b-button @click="bet(5)">Bet 5</b-button> | <b-button @click="bet(15)">Bet 15</b-button> | <b-button @click="bet(25)">Bet 25</b-button> 
                      </div>
                      <div v-if="reset">
                          <b-button @click="resetGame()">Next Hand</b-button>
                        </div>
                  </div>
                  <div class="col-sm-12" style="padding-top: 40px;" v-else>
                   <strong> Game over. You're out of money.</strong> 
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
  data () {
    return {
      deck: [],
      hand: [],
      gameOn: false,
      betValue: 0,
      changeCards: false,
      money: 100,
      win: false,
      noWin: false,
      winner: [{ name: null, value: 0 }],
      firstDeal: true, 
      reset: false,
      noMoney: false
    }
  },
  created () {
    this.buildEmptyHand()
  },
  methods: {
    resetGame(){
      this.hand.forEach(card => {
          card.disabled = true
          card.discarded = true
          card.name = 'back.png'
        });
      
      this.money += (this.betValue * this.winner.value)

      if(this.money < 4){
        this.noMoney =true
      }
      this.gameOn = false
      this.reset = false
      this.deck=[]
      this.noWin = false
      this.win = false
    },
    buildDeck () {
      let x = 1
      let i = 1
      for (x = 1; x < 5; x++) {
        for (i = 1; i < 14; i++) {
          this.deck.push((x * 100) + i)
        }
      }
    },
    buildEmptyHand () {
      for (let i = 0; i < 5; i++) {
        this.hand.push({ name: 'back.png', face: 0, suit: 0, discarded: true, disabled: true })
      }
    },
    bet (value) {
      this.betValue = value
      this.money -= value
      this.deal()
    },
    deal () {


      if(this.firstDeal == true){
        this.buildDeck()
      }else{
        this.reset = true
      }
      this.firstDeal = !this.firstDeal
      // game status
      this.gameOn = true
      let handcomplete = 0
      let currentCard = []

      // populate hand
      this.hand.forEach(card => {
        if(card.discarded == true){
        currentCard = this.drawCard()
        card.suit = currentCard[0]
        card.face = currentCard[1]
        card.name = (currentCard[0] * 100) + currentCard[1] + '.png'
        card.disabled = false
        card.discarded = false
        }
      });

      this.checkResults()

      // discard cards
      this.changeCards = !this.changeCards
    },
    drawCard () {
      let suit = 0
      let face = 0
      let index = _.random(0, (this.deck.length-1)) 
      let selectedCard = this.deck[index]
      if (selectedCard > 400) {
        suit = 4
        face = selectedCard - 400
      } else if (selectedCard > 300) {
        suit = 3
        face = selectedCard - 300
      } else if (selectedCard > 200) {
        suit = 2
        face = selectedCard - 200
      } else if (selectedCard > 100) {
        suit = 1
        face = selectedCard - 100
      }
      for (var i = 0; i < this.deck.length; i++) {
        if (this.deck[i] == selectedCard) {
          this.deck.splice(i, 1)
          i--
        }
      }

      let cardNow = [suit, face]
      return cardNow
    },
    checkResults () {
      let faces = []
      let suits = []
      let temp = 0

      let twoPair = false
      let threeKind = false
      let pair = false
      let flush = true
      let strait = false
      let straitFlush = false
      let royalFlush = false
      let fourOfaKind = false
      let fullHouse = false

      this.hand.forEach(card => {
        suits.push(card.suit)
        temp = card.face
        if (card.face == 1) {
          temp = 14
        }
        faces.push(temp)
      })

      // check flush
      let i = 0
      for (i = 0; i < 5; i++) {
        if (suits[i] != suits[0]) {
          flush = false
        }
      }

      // check strait
      faces = _.sortBy(faces)

      for (i = 0; i < 4; i++) {
        if ((faces[i] + 1) == faces[i + 1]) {
          strait = true
        } else {
          strait = false
          i = 5
        }
      }

      if (strait == true && flush == true) {
        if (faces[4] == 14) {
          royalFlush = true
        } else {
          straitFlush = true
        }
      }

      // four of a kind
      if (faces[0] == faces[1] && faces[1] == faces[2] && faces[2] == faces[3]) {
        fourOfaKind = true
      } else if (faces[1] == faces[2] && faces[2] == faces[3] && faces[3] == faces[4]) {
        fourOfaKind = true
      }

      // fullhouse
      if (faces[0] == faces[1] && faces[1] == faces[2] && faces[3] == faces[4]) {
        fullHouse = true
      } else if (faces[0] == faces[1] && faces[2] == faces[3] && faces[3] == faces[4]) {
        fullHouse = true
      }

      // three of a kind
      if (faces[0] == faces[1] && faces[1] == faces[2]) {
        threeKind = true
      } else if (faces[1] == faces[2] && faces[2] == faces[3]) {
        threeKind = true
      } else if (faces[2] == faces[3] && faces[3] == faces[4]) {
        threeKind = true
      }
      // 2 pair
      if (faces[0] == faces[1] && faces[2] == faces[3]) {
        twoPair = true
      } else if (faces[1] == faces[2] && faces[3] == faces[4]) {
        twoPair = true
      }else if(faces[0] == faces[1] && faces[3] == faces[4]){
        twoPair = true
      }
      // pair
      for (i = 0; i < 4; i++) {
        if (faces[i] == faces[i + 1]) {
          if (faces[i] > 10) {
            pair = true
          }
        }
      }

      if (royalFlush == true) {
        this.winner.name = 'Royal Flush!!!! OMG!!!!!!! WOW!!!!'
        this.winner.value = 500
        this.win = true
      } else if (straitFlush == true) {
        this.winner.name = 'Strait Flush!'
        this.winner.value = 200
        this.win = true
      } else if (fourOfaKind == true) {
        this.winner.name = '4 of a kind!'
        this.winner.value = 100
        this.win = true
      } else if (fullHouse == true) {
        this.winner.name = 'Full House!'
        this.winner.value = 50
        this.win = true
      } else if (strait == true) {
        this.winner.name = 'Strait!'
        this.winner.value = 20
        this.win = true
      } else if (flush == true) {
        this.winner.name = 'Flush!'
        this.winner.value = 10
        this.win = true
      } else if (threeKind == true) {
        this.winner.name = 'Three of a kind!'
        this.winner.value = 5
        this.win = true
      } else if (twoPair == true) {
        this.winner.name = 'Two pairs!'
        this.winner.value = 2
        this.win = true
      } else if (pair == true) {
        this.winner.name = 'Jacks or better!'
        this.winner.value = 1
        this.win = true
      }else{
        if(this.firstDeal){
          this.winner.name = null
          this.winner.value = 0
          this.noWin = true
          this.win = false
        }
      }

      // console.log(faces)
      // console.log('2 pair   ' + twoPair)
      // console.log('3   ' + threeKind)
      // console.log('pair   ' + pair)
      // console.log('FH  ' + fullHouse)
      // console.log('FoAk  ' + fourOfaKind)
      // console.log('sf  ' + straitFlush)
      // console.log('rf  ' + royalFlush)
      // console.log('str  ' + strait)
      // console.log('fl   ' + flush)
    },
    discard (index) {
      if (this.hand[index].discarded == false) {
        this.hand[index].name = 'back.png'
        this.hand[index].discarded = true
      } else {
        this.hand[index].name = (this.hand[index].suit * 100) + this.hand[index].face + '.png'
        this.hand[index].discarded = false
      }
    }
  },
  
}
</script>
