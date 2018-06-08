<template>
  <section class="code">
<div class="stats_page">
  <div>
    <h1 v-if="record">Record : {{ record.points }} - {{ record.name }} </h1>
  </div>
  <v-text-field
      append-icon="search"
      label="Search"
      single-line
      hide-details
      v-model="search"
    ></v-text-field>
  <v-data-table
    v-if="allScores"
    :search="search"
    :headers="headers"
    :items="allScores"
    :rows-per-page-items="rowsPerPageItems"
    :pagination.sync="pagination"
    class="text-xs-left scroll elevation-1 scroll-x"
  >
  <template slot="items" scope="props">
    <td>{{ props.item.player.nickName }}</td>
    <td class="text-xs-left">{{ props.item.total }}</td>
    <td class="text-xs-left">{{ props.item.ace }}</td>
    <td class="text-xs-left">{{ props.item.two }}</td>
    <td class="text-xs-left">{{ props.item.three }}</td>
    <td class="text-xs-left">{{ props.item.four }}</td>
    <td class="text-xs-left">{{ props.item.five }}</td>
    <td class="text-xs-left">{{ props.item.six }}</td>
    <td class="text-xs-left">{{ props.item.totalTop }}</td>
    <td class="text-xs-left">{{ props.item.lowest }}</td>
    <td class="text-xs-left">{{ props.item.highest }}</td>
    <td class="text-xs-left">{{ props.item.totalDiff }}</td>
    <td class="text-xs-left">{{ props.item.smallStraight }}</td>
    <td class="text-xs-left">{{ props.item.highStraight }}</td>
    <td class="text-xs-left">{{ props.item.totalStraights }}</td>
    <td class="text-xs-left">{{ props.item.threeOfAKind }}</td>
    <td class="text-xs-left">{{ props.item.fullHouse }}</td>
    <td class="text-xs-left">{{ props.item.fourOfAKind }}</td>
    <td class="text-xs-left">{{ props.item.yams }}</td>
    <td class="text-xs-left">{{ props.item.bonusYams }}</td>
    <td class="text-xs-left">{{ props.item.yamsSec }}</td>
    <!-- <td class="last-td text-xs-left">{{ props.item.winner ? 'oui' : 'non' }}</td> -->
  </template>
</v-data-table>

</div>
  </section>
</template>

<script>
// import createPlayerGql from '~/apollo/createPlayer.graphql'
import allScores from '~/apollo/allScores.graphql'
import record from '~/apollo/record.graphql'

export default {
  name: 'stats',
  components: {
  },
  data: () => ({
    record: '',
    rowsPerPageItems: [10, 20, 30, 50, 200],
    pagination: {
      rowsPerPage: 20
    },
    search: '',
    headers: [
      {
        text: 'Joueur',
        sortable: false,
        value: 'player.nickName'
      },
      { text: 'Total', value: 'total', align: 'left' },
      { text: 'Les 1', value: 'ace', align: 'left' },
      { text: 'Les 2', value: 'two', align: 'left' },
      { text: 'Les 3', value: 'three', align: 'left' },
      { text: 'Les 4', value: 'four', align: 'left' },
      { text: 'Les 5', value: 'score.top.five', align: 'left' },
      { text: 'Les 6', value: 'six', align: 'left' },
      { text: 'Total haut', value: 'totalTop', align: 'left' },
      { text: 'moins', value: 'lowest', align: 'left' },
      { text: 'plus', value: 'highest', align: 'left' },
      { text: 'Total diff', value: 'totalDiff', align: 'left' },
      { text: 'Petite Suite', value: 'smallStraight', align: 'left' },
      { text: 'Grande Suite', value: 'highStraight', align: 'left' },
      { text: 'Total suites', value: 'totalStraights', align: 'left' },
      { text: 'Brelan', value: 'threeOfAKind', align: 'left' },
      { text: 'Full', value: 'fullHouse', align: 'left' },
      { text: 'Carré', value: 'fourOfAKind', align: 'left' },
      { text: 'Yams', value: 'yams', align: 'left' },
      { text: 'Bonus Yams', value: 'bonusYams', align: 'left' },
      { text: 'Yams Sec', value: 'yamsSec', align: 'left' }
      // { text: 'Gagnant', value: 'winner', align: 'left' }
    ]
  }),
  methods: {
  },
  apollo: {
    allScores: {
      query: allScores,
      prefetch: true
    },
    record: {
      query: record,
      update (data) {
        if (data.allScores[0].total > 599) {
          return { points: data.allScores[0].total, name: data.allScores[0].player.nickName }
        } else {
          return { points: 599, name: 'Nono' }
        }
      }
    }
  }
}
</script>

<style lang="scss" >
.code {
  display: flex;
  justify-content: center;
  position: relative;
  height: 100%;
}

</style>
