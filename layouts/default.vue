<template>
  <div>
    <div class="header">
      <router-link class="header__logo" to="/">
        <span>Yams</span>
      </router-link>
      <span class="spacer"></span>

      <v-flex xs3 sm3>
      <v-select v-if="allPlayers"
            style="{ background-color: blue }"
            content-class="select-dropdown"
            label="Joueurs"
            v-bind:items="allPlayers"
            v-model="playing"
            item-text="nickName"
            multiple
            @change="reset"
          ></v-select>
          </v-flex>
      <div class="header__menu">

        <div class="link" @click="startGame">
          Jouer
        </div>
        <span class="spacer"></span>

        <div class="link" @click="nextGame">
          Partie suivante
        </div>
        <div class="link" @click="reload">
          Charger partie
        </div>
        <router-link class="link" :to="{ name: 'createPlayer' }" exact>
          Créer joueur
        </router-link>
        <span class="spacer"></span>
        <router-link class="link" :to="{ name: 'stats' }" exact>
          Stats
        </router-link>
        <v-menu bottom left>
           <v-btn icon slot="activator" dark>
             <v-icon>settings</v-icon>
           </v-btn>
           <v-list>
             <v-list-tile v-for="item in items" :key="item.title" @click="">
               <v-list-tile-title>{{ item.title }}</v-list-tile-title>
             </v-list-tile>
           </v-list>
         </v-menu>
      </div>
    </div>

    <section class="main">
      <div class="main__content">
        <nuxt :playing="playing"/>
      </div>
    </section>
  </div>
</template>

<script>
import Panel from '~/components/Panel.vue'
import Search from '~/components/Search.vue'
import allPlayers from '~/apollo/allPlayers.graphql'
import createGameGql from '~/apollo/createGame.graphql'
import createScore from '~/apollo/createScore.graphql'
import allGames from '~/apollo/allGames.graphql'

import {mapActions, mapGetters} from 'vuex'
import _ from 'lodash'

export default {
  components: {
    Panel,
    Search
  },
  data: () => ({
    items: [
      { title: 'Click Me' },
      { title: 'Click Me' },
      { title: 'Click Me' },
      { title: 'Click Me 2' }
    ],
    playing: [],
    winner: '',
    gameId: ''
  }),
  computed: {
    ...mapGetters(['players']),
    playersIds () {
      let arr = []
      _.forEach(this.playing, (player) => {
        arr.push(player.id)
      })
      return arr
    }
  },
  methods: {
    ...mapActions(['addPlayer', 'reset', 'setPlayers']),
    createGame () {
      this.$apollo.mutate({
        mutation: createGameGql,
        variables: {
          playing: this.playersIds.length,
          playersIds: this.playersIds
        },
        update: (store, { data: { createGame } }) => {
          try {
            const data = store.readQuery({
              query: allGames
            })
            data.allGames.push(createGame)
            store.writeQuery({ query: allGames, data })
            this.gameId = data.allGames[data.allGames.length - 1].id
          } catch (e) {
            console.log(e)
          }
        }
      })
    },
    createScore () {
      _.forEach(this.players, (player) => {
        this.$apollo.mutate({
          mutation: createScore,
          variables: {
            ace: player.score.top.one,
            two: player.score.top.two,
            three: player.score.top.three,
            four: player.score.top.four,
            five: player.score.top.five,
            six: player.score.top.six,
            totalTop: player.totalTop,
            lowest: player.score.diff.lowest,
            highest: player.score.diff.highest,
            totalDiff: player.totalDiff,
            smallStraight: player.score.straights.small,
            highStraight: player.score.straights.high,
            totalStraights: player.totalDiff,
            threeOfAKind: player.score.bottom.threeOfAKind,
            fullHouse: player.score.bottom.fullHouse,
            fourOfAKind: player.score.bottom.fourOfAKind,
            yams: player.score.bottom.yams,
            bonusYams: player.score.bottom.bonusYams,
            yamsSec: player.score.bottom.yamsSec,
            totalBottom: player.totalBottom,
            total: player.score.total,
            winner: false,
            playerId: player.id,
            gameId: this.gameId
          }
        })
      })
    },
    startGame () {
      this.createGame()
      _.forEach(this.playing, (player) => {
        this.addPlayer(
          {
            name: player.nickName,
            id: player.id,
            winner: false,
            score: {
              top: {
                one: null,
                two: null,
                three: null,
                four: null,
                five: null,
                six: null
              },
              diff: {
                lowest: null,
                highest: null
              },
              straights: {
                small: null,
                high: null
              },
              bottom: {
                threeOfAKind: null,
                fullHouse: null,
                fourOfAKind: null,
                yams: null,
                bonusYams: 0,
                yamsSec: 0
              },
              total: null,
              totalTop: null,
              totalDiff: null,
              totalStraights: null,
              totalBottom: null
            }
          }
        )
      })
    },
    whoWins () {
      let score = 0
      let winning = ''
      _.forEach(this.players, (player) => {
        if (player.score.total > score) {
          score = player.score.total
          winning = player.name
        }
        this.winner = winning
      })
    },
    nextGame () {
      this.createScore()
      this.whoWins()
      _.forEach(this.players, (player) => {
        player.name === this.winner ? player.winner = true : player.winner = false
      })
      /*
      axios.post('https://api.mlab.com/api/1/databases/yams/collections/scores?apiKey=Abe_aqSvB_QidC68ajjmEsIWU6clrskh', this.players)
        .then(function (response) {
          console.log(response)
        })
        .catch(function (error) {
          console.log(error)
        })
      */
      let idxFirstPlayer = ''
      _.forEach(this.players, (player) => {
        if (player.name === this.winner) {
          idxFirstPlayer = idxFirstPlayer === this.players.length - 1 ? 0 : this.players.indexOf(player) + 1
          // alert(idxFirstPlayer)
        }
      })
      const newOrder = this.players.slice(idxFirstPlayer, this.players.length)
      this.players.splice(idxFirstPlayer, this.players.length - 1)
      for (let i = newOrder.length - 1; i >= 0; i -= 1) {
        this.players.unshift(newOrder[i])
      }
      this.setPlayers()
      this.createGame()
    },
    reload () {
      this.reset()
      for (let i = 0; i < localStorage.length; i += 1) {
        this.addPlayer(JSON.parse(localStorage.getItem(localStorage.key(i))))
      }
    }
  },
  apollo: {
    allPlayers: {
      query: allPlayers,
      prefetch: true
    },
    allGames: {
      query: allGames
    }
  }
}
</script>

<style lang="scss">
@import 'assets/variables.scss';
.menu__content {
  background-color: red;
  top:60px;
  left: 0px;
}
.main {
  background-image: url('https://images.alphacoders.com/257/257863.jpg');
  background-size: cover;
  background-repeat:no-repeat;
  width: 100%;
  min-height: calc(100vh - 60px);
  display: flex;
  &__content {
    padding: 16px;
    flex: 1;
  }
  &__panel {
    width: 300px;
    min-height: 100%;
    background: #F3F3F3;
  }
}

.header {
  background-color: #fff;
  height: 60px;
  padding: 10px 16px;
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 2;
  border-bottom: 1px solid #eee;
  &__menu {
    display: flex;
    justify-content: center;
    align-items: center;
    margin-left: auto;
    .spacer {
      width: 1px;
      height: 30px;
      margin: 16px;
      background: #eee;
    }
    .link {
      cursor: pointer;
      padding: 16px;
      color: $link;
      &:hover {
        color: $linkHover;
      }
      &.nuxt-link-active {
        color: darken($primary, 3%);
      }
    }
    .field {
      width: 250px;
    }
  }
  &__logo {
    display: inline-block;
    font-size: 1.5em;
    color: #2c3e50;
    font-family: 'Dosis', 'Source Sans Pro', 'Helvetica Neue', Arial, sans-serif;
    font-weight: 500;
  }
}

.page-enter-active, .page-leave-active {
  transition: all .2s;
}
.page-enter {
  opacity:   0;
}
</style>
