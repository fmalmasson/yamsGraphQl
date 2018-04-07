<template>
  <section class="code">
    <v-flex class="player" xs2 v-for="(player, index) in players">
    <player @save="saveGame()"
    :index="index"
    :player="player"
    :key="index"></player>
  </v-flex>
  </section>
</template>

<script>
// import createPlayerGql from '~/apollo/createPlayer.graphql'
import {mapGetters} from 'vuex'
import Player from './../components/Player'
import _ from 'lodash'

export default {
  head: {
    title: 'Create snippet'
  },
  components: {
    Player
  },
  data () {
    return {
      winner: '',
      data: {
        nickName: '',
        firstName: '',
        lastName: ''
      }
    }
  },
  computed: {
    ...mapGetters(['players'])
  },
  methods: {
    saveGame () {
      localStorage.clear()
      _.forEach(this.players, (player) => localStorage.setItem(player.name, JSON.stringify(player)))
    }
  }
}
</script>

<style lang="scss">
.code {
  display: flex;
  justify-content: left;
  position: relative;
  height: 90%;
  &__actions {
    position: absolute;
    top: 0px;
    right: 0px;
    .checkbox {
      cursor: pointer;
      margin-right: 16px;
      label {
        cursor: pointer;
      }
    }
  }
  textarea {
    font-size: 16px;
    color: #555;
    border: 0;
    width: 100%;
    height: 100%;
    resize: none;
  }
}

</style>
