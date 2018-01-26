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
    <td class="last-td text-xs-left">{{ props.item.winner }}</td>
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
    pagination: {
      sortBy: 'total'
    },
    search: '',
    headers: [
      {
        text: 'Joueur',
        sortable: false,
        value: 'allScores.player.nickName'
      },
      { text: 'Total', value: 'allScores.total', align: 'left' },
      { text: 'Les 1', value: 'allScores.ace', align: 'left' },
      { text: 'Les 2', value: 'allScores.two', align: 'left' },
      { text: 'Les 3', value: 'allScores.three', align: 'left' },
      { text: 'Les 4', value: 'allScores.four', align: 'left' },
      { text: 'Les 5', value: 'score.top.five', align: 'left' },
      { text: 'Les 6', value: 'allScores.six', align: 'left' },
      { text: 'Total haut', value: 'allScores.totalTop', align: 'left' },
      { text: 'moins', value: 'allScores.lowest', align: 'left' },
      { text: 'plus', value: 'allScores.highest', align: 'left' },
      { text: 'Total diff', value: 'allScores.totalDiff', align: 'left' },
      { text: 'Petite Suite', value: 'allScores.smallStraight', align: 'left' },
      { text: 'Grande Suite', value: 'allScores.highStraight', align: 'left' },
      { text: 'Total suites', value: 'allScores.totalStraights', align: 'left' },
      { text: 'Brelan', value: 'allScores.threeOfAKind', align: 'left' },
      { text: 'Full', value: 'allScores.fullHouse', align: 'left' },
      { text: 'Carré', value: 'allScores.fourOfAKind', align: 'left' },
      { text: 'Yams', value: 'allScores.yams', align: 'left' },
      { text: 'Bonus Yams', value: 'allScores.bonusYams', align: 'left' },
      { text: 'Yams Sec', value: 'allScores.yamsSec', align: 'left' },
      { text: 'Winner', value: 'allScores.winner', align: 'left' }
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
