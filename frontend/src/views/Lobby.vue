<template>
  <div id="lobby">
    <new-game :currentUser="currentUser" @newGame="populateGames"></new-game>
    <game-list title="Open Games" :games="openGames" :currentUser="currentUser" @joinGame="populateGames"></game-list>
    <game-list title="Pending Games" :games="pendingGames" :currentUser="currentUser"></game-list>
  </div>
</template>

<script>
import NewGame from '@/components/NewGame'
import GameList from '@/components/GameList'
import auth from '../auth'
import api from '../api'

export default {
  name: 'lobby',
  props: ['currentUser'],
  components: {
    NewGame,
    GameList
  },
  data() {
    return {
      pendingGames: [],
      openGames: []
    }
  },
  methods: {
    populateGames() {
      const authToken = auth.getToken();
      const fetchConfigGet = api.fetchConfigGet(authToken);
      const userId = this.currentUser.id;

      fetch(`${process.env.VUE_APP_REMOTE_API}/pendingGames/${userId}`, fetchConfigGet
      )
      .then((response) => {
        return response.json();
      })
      .then((games) => {
        this.pendingGames = games;
      });
      
      fetch(`${process.env.VUE_APP_REMOTE_API}/openGames/${userId}`, fetchConfigGet
      )
      .then((response) => {
        return response.json();
      })
      .then((games) => {
        this.openGames = games;
      });
    }
  },
  watch: {
    currentUser: function() {
      this.populateGames();
    }
  },
  created() {
      this.populateGames();
  }
}
</script>

<style scoped>
#lobby > * {
  margin: 2rem;
}
</style>