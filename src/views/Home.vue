<template>
  <v-layout column justify-center align-center>
    <v-row xs12 sm8 md12 style="max-width: 88rem">
      <v-col v-for="module in modules" :key="module.id"
        cols="12">
        <h2>
          <router-link :to="`/module/${module.id}`" class="nolink"
                      style="text-decoration: none; color: white;">
            {{ module.name }}
          </router-link>
        </h2>
        <v-row justify="start">
          <SessionCard
            v-for="session in getSessionsByModuleId(module.id)"
            :key="session.id"
            :session="session"
          ></SessionCard>
        </v-row>
      </v-col>
    </v-row>
  </v-layout>
</template>

<script>
import { mapActions, mapState, mapGetters } from 'vuex'
import SessionCard from '../components/SessionCard.vue'

export default {
  components: {
    SessionCard
  },
  name: 'Home',
  async mounted () {
    await this.fetchModules() // une erreur ici ne gardez pas ça tel quel
    await Promise.all(
      this.modules.map(m => this.fetchSessionsForModule({ moduleId: m.id }))
    )
    await Promise.all(
      this.sessions.map(s => this.fetchExercisesForSession({ sessionId: s.id }))
    )
  },
  computed: {
    ...mapState('modules', ['modules']),
    ...mapState('sessions', ['sessions']),
    ...mapState('exercises', ['exercises']),
    ...mapGetters('sessions', ['getSessionsByModuleId'])
  },
  methods: {
    ...mapActions('modules', ['fetchModules']),
    ...mapActions('sessions', ['fetchSessionsForModule']),
    ...mapActions('exercises', ['fetchExercisesForSession'])
  }
}
</script>
<style lang="css">
.nolink {
}
</style>
