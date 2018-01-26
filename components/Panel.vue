<template>
  <div class="panel">
    <div class="panel__title">Last public snippets</div>
    <div v-for="code in allCodes" :key="code.id" @click="$router.push({ name: 'code', params: { code: code.id } })" class="panel__item" >
      <h6>{{ code.name | placeholder(code.content) | truncate(40) }}</h6>
      <small>{{ code.type }}, {{ code.createdAt | formatDate }}</small>
    </div>
  </div>
</template>

<script>
import allPlayers from '~/apollo/allPlayers.graphql'

export default {
  apollo: {
    allPlayers: {
      query: allPlayers,
      update ({ allPlayers }) {
        return allPlayers
      }
    }
  }
}
</script>

<style lang="scss">
.panel {
  &__title {
    padding: 16px;
    font-size: 14px;
    color: #333;
    text-transform: uppercase;
  }
  &__item {
    padding: 8px 16px;
    cursor: pointer;
    h6 {
      color: #777;
      font-size: 14px;
    }
    &:hover {
      background: #e5e5e5;
    }
  }
}
</style>
