<template>
  <div class="gamecover">
    <div class="overlay" v-if="playerA.img && playerB.img" @click="clearHand()"></div>
    <div class="note">
      {{ note }}
      <div class="deck">Total cards in deck : {{ deckData.length }}</div>
      <span v-if="playerA.img && playerB.img">Click on anywhere to start next round</span>
      <div class="restart" v-if="deckData.length == 0 && !playerA.img && !playerB.img" @click="setAllCard()">Restart</div>
    </div>
    <div class="playground">
      <div class="player a" :class="{'win' : playerA.status}">
        <div class="card" v-if="playerA.img"><img :src="`./src/assets/${playerA.img}.svg`" /></div>
        <div class="text"><span>Player A</span>Score : {{ playerA.point }}</div>
        <div class="btn" :class="{'disabled' : playerA.img || deckData.length == 0}" @click="drawCard(0)">DRAW</div>
      </div>
      <div class="player b" :class="{'win' : playerB.status}">
        <div class="card" v-if="playerB.img"><img :src="`./src/assets/${playerB.img}.svg`" /></div>
        <div class="text"><span>Player B</span>Score : {{ playerB.point }}</div>
        <div class="btn" :class="{'disabled' : playerB.img || deckData.length == 0}" @click="drawCard(1)">DRAW</div>
      </div>  
    </div>
  </div>
</template>

<script>
export default {
    data(){
        return {
            'typeData' : [
              'diamonds', 
              'clubs', 
              'hearts', // missing image
              'spades'
            ],
            'cardData' : [
              '2', '3', '4', '5', '6', '7', '8', '9', '10', 'jack', 'queen', 'king', 'ace'
            ],
            'deckData': [],
            'playerA' : {
              'point' : 0,
              'card'  : '',
              'type'  : '',
              'img'   : '',
              'status': false
            },
            'playerB' : {
              'point' : 0,
              'card'  : '',
              'type'  : '',
              'img'   : '',
              'status': false
            },
            'note'    : '',
            'step'    : 0
        };
    },
    created () {
        this.setAllCard();
    },
    mounted() {
        
    },
    destroyed(){
    },
    methods:{
      setAllCard()
      {
        this.note = 'Draw A Card';
        this.deckData = [];
        this.typeData.map((type, tindex) =>
        {
          this.cardData.map((card, cindex) =>
          {
            this.deckData.push({
              'img'      : `${type}_${card}`,
              'typeindex': tindex,
              'cardindex': cindex
            });
          });
        });
      },
      shuffleDeck()
      {
        this.deckData = this.deckData.sort(() => {
          return Math.random() - 0.5;
        });
      },
      drawCard(player)
      {
        let index = Math.floor(Math.random() * (this.deckData.length - 1));
        let dataObj = this.deckData[index];
        if(player == 0)
        {
          this.playerA.card = dataObj.cardindex;
          this.playerA.type = dataObj.typeindex;
          this.playerA.img = dataObj.img;
        }
        else
        {
          this.playerB.card = dataObj.cardindex;
          this.playerB.type = dataObj.typeindex;
          this.playerB.img = dataObj.img;
        }

        this.deckData.splice(index, 1);
        this.compareCard();
      },
      compareCard()
      {
        if(this.playerA.img && this.playerB.img)
        {
          if(this.playerA.card > this.playerB.card)
          {
            this.note = `Congrat, Player A win this round. Player A +1 point`;
            this.playerA.point += 1;
            this.playerA.status = true;
          }
          else if(this.playerA.card == this.playerB.card)
          {
            this.compareType();
          }
          else
          {
            this.note = `Congrat, Player B win this round. Player B +1 point`;
            this.playerB.point += 1;
            this.playerB.status = true;
          }
        }
      },
      compareType()
      {
        if(this.playerA.type > this.playerB.type)
        {
          this.note = `Congrat, Player A win this round.`;
          this.playerA.point += 1;
          this.playerA.status = true;
        }
        else
        {
          this.note = `Congrat, Player B win this round.`;
          this.playerB.point += 1;
          this.playerB.status = true;
        }
      },
      clearHand()
      {
        this.playerA.card = '';
        this.playerB.card = '';
        this.playerA.img = '';
        this.playerA.status = false;

        this.playerA.type = '';
        this.playerB.type = '';
        this.playerB.img = '';
        this.playerB.status = false;

        this.step = 0;
        this.note = 'Draw A Card';

        if(this.deckData.length == 0)
        {
          if(this.playerA.point > this.playerB.point)
          {
            this.note = `Game Over, The Winner is Player A`;
          }
          else if(this.playerA.point < this.playerB.point)
          {
            this.note = `Game Over, The Winner is Player B`;
          }
          else
          {
            this.note = 'Game Over, There is a Draw';
          }
          
        }
      },
      restart()
      {
        this.setAllCard();
      }
    }
}

</script>

<style scoped lang="scss">
.gamecover
{
  display           : block;
  position          : relative;
  width             : 100%;
  height            : 100vh;
  padding           : 20px;
  box-sizing        : border-box;
  -webkit-box-sizing: border-box;

  & > .overlay
  {
    display : block;
    position: absolute;
    width   : 100%;
    height  : 100%;
    top     : 0px;
    left    : 0px;
    z-index : 9999;
  }
  & > .note
  {
    display              : flex;
    width                : 100%;
    height               : calc(50% - 10px);
    margin               : 0px 0px 20px 0px;
    justify-content      : center;
    align-items          : center;
    position             : relative;
    box-sizing           : border-box;
    -webkit-box-sizing   : border-box;
    background           : rgba(0,0,0,0.05);
    border-radius        : 8px;
    -webkit-border-radius: 8px;
    font-size            : 18px;

    & > span
    {
      display   : block;
      position  : absolute;
      width     : 100%;
      text-align: center;
      font-size : 14px;
      opacity   : 0.4;
      bottom    : 20px;
      left      : 0px;
    }
    & > .deck
    {
      display  : block;
      position : absolute;
      top      : 20px;
      right    : 20px;
      font-size: 20px;
    }
    & > .restart
    {
      display              : block;
      position             : absolute;
      width                : 140px;
      text-align           : center;
      bottom               : 40px;
      left                 : calc(50% - 70px);
      line-height          : 50px;
      height               : 50px;
      background           : rgba(0,0,0,0.1);
      font-size            : 16px;
      font-weight          : bold;
      text-transform       : uppercase;
      border-radius        : 10px;
      -webkit-border-radius: 10px;
      cursor               : pointer;
    }
  }
  & > .playground
  {
    display : block;
    position: relative;
    width   : 100%;
    height  : calc(50% - 10px);

    & > .player
    {
      display              : flex;
      justify-content      : center;
      align-items          : center;
      position             : relative;
      float                : left;
      width                : calc(50% - 10px);
      height               : 100%;
      box-sizing           : border-box;
      -webkit-box-sizing   : border-box;
      background           : rgba(0,0,0,0.05);
      border-radius        : 8px;
      -webkit-border-radius: 8px;

      &:first-child
      {
        margin:0px 20px 0px 0px;
      }
      &.win
      {
        & > .text
        {
          color:#6ce06c;
        }
      }
      & > .text
      {
        display            : block;
        position           : relative;
        width              : 100%;
        text-align         : center;
        font-size          : 20px;
        user-select        : none;
        -webkit-user-select: none;
        
        & > span
        {
          display    : block;
          position   : relative;
          width      : 100%;
          font-size  : 40px;
          font-weight: bold;
          line-height: 40px;
          margin     : 0px 0px 20px 0px;
        }
      }
      & > .card
      {
        display    : block;
        position   : absolute;
        width      : 140px;
        line-height: 0px;
        top        : -90px;
        padding    : 0px;
        left       : calc(50% -  70px);
        z-index    : 1;
        
        & > img
        {
          display : block;
          position: relative;
          width   : 100%;
        }
      }
      & > .btn
      {
        display              : block;
        position             : absolute;
        z-index              : 1;
        width                : 180px;
        height               : 50px;
        text-align           : center;
        line-height          : 50px;
        font-size            : 18px;
        font-weight          : bold;
        background           : rgba(0,0,0,0.1);
        border-radius        : 10px;
        -webkit-border-radius: 10px;
        bottom               : 40px;
        left                 : calc(50% - 90px);
        cursor               : pointer;

        &.disabled
        {
          pointer-events: none;
          opacity       : 0.4;
        }
      }
    }
  }
}
</style>
