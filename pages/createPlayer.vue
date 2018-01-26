<template>
  <section class="code">
    <v-form class="form" ref="form">
    <v-text-field
      label="Pseudo"
      v-model="player.nickName"
      :counter="10"
      required
    ></v-text-field>
    <v-text-field
      label="Prénom"
      v-model="player.firstName"
      required
    ></v-text-field>
    <v-text-field
      label="Nom"
      v-model="player.lastName"
      required
    ></v-text-field>
        <v-btn
      @click="createPlayer"
    >
      Valider
    </v-btn>
    <v-btn @click="createPlayer">Effacer</v-btn>
  </v-form>
  </section>
</template>

<script>
import createPlayerGql from '~/apollo/createPlayer.graphql'
import allPlayers from '~/apollo/allPlayers.graphql'

export default {
  components: {
  },
  data: () => ({
    player: {
      nickName: '',
      firstName: '',
      lastName: ''
    }
  }),
  methods: {
    clear () {
      this.$refs.form.reset()
    },
    createPlayer () {
      this.$apollo.mutate({
        mutation: createPlayerGql,
        variables: {
          ...this.player
        },
        update: (store, { data: { createPlayer } }) => {
          const data = store.readQuery({
            query: allPlayers
          })
          data.allPlayers.push(createPlayer)
          store.writeQuery({ query: allPlayers, data })
        }
      }).then((data) => {
        this.$router.push({ name: 'index' })
      }).catch((error) => {
        console.error(error)
      })
    }
  }
}
</script>

<style lang="scss">
.code {
  display: flex;
  justify-content: center;
  position: relative;
  height: 100%;
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
  input {
    border: none;
    color: black;
    background-color: white;
  }
  label {
    padding-left: 5px;
  }
  form {
    justify-content: center;
  }
}

</style>
