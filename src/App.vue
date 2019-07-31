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
                <div class="col-sm">
                  <img src="./assets/images/101.png">
                </div>
                <div class="col-sm">
                    <img src="./assets/images/101.png">
                </div>
                <div class="col-sm">
                    <img src="./assets/images/101.png">
                </div>
                <div class="col-sm">
                    <img src="./assets/images/101.png">
                </div>
                  <div class="col-sm">
                      <img src="./assets/images/101.png">
                  </div>
              </div>
              <div class="row">
                  <div class="col-sm-12" style="padding-top: 40px;">
                     text here
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
}
</script>


