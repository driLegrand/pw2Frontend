<template>
  <v-layout column justify-center align-center>
    <v-row xs12 sm8 md12 class="container">
      <v-col cols="12">
        <h1> {{ module.name }}</h1>
      </v-col>
    </v-row>
    <v-row xs12 sm8 md12 style="max-width: 88rem">
      <v-col v-for="session in sessions" :key="session.id"
        cols="12">
        <h2>
          <router-link :to="`/session/${session.id}/do`"
            style="color: white; text-decoration: none">
            {{ session.name }}
          </router-link>
        </h2>
        <v-row justify="start">
          <router-link v-for="exercise of getExercisesBySessionId(session.id)"
            :key="exercise.id"
            :to="`/session/${session.id}/do/${exercise.id}`"
            style="color: white; text-decoration: none">
            <ExerciseCard
              :exercise="exercise"
              :session="session"
              height="8em"
              width="16em"
              ></ExerciseCard>
          </router-link>
        </v-row>
      </v-col>
    </v-row>
  </v-layout>
</template>

<script>
import { mapActions, mapState, mapGetters } from 'vuex'
import ExerciseCard from '../components/ExerciseCard.vue'

export default {
  components: {
    ExerciseCard
  },
  async mounted () {
    await this.fetchModule({ id: this.moduleId })
    await Promise.all(
      this.modules.map(m =>
        this.fetchSessionsForModule({ moduleId: m.id }))
    )
    await Promise.all(
      this.sessions.map(s =>
        this.fetchExercisesForSession({ sessionId: s.id }))
    )
  },
  computed: {
    ...mapState('modules', ['modules']),
    ...mapGetters('modules', ['getModuleById']),
    ...mapGetters('sessions', ['getSessionsByModuleId']),
    ...mapGetters('exercises', ['getExercisesBySessionId']),
    module () {
      return this.getModuleById(this.moduleId) ||
        { name: 'Loading..' }
    },
    moduleId () {
      return parseInt(this.$route.params.id)
    },
    sessions () {
      return this.getSessionsByModuleId(this.moduleId)
    }
  },
  methods: {
    ...mapActions('modules', ['fetchModule']),
    ...mapActions('sessions', ['fetchSessionsForModule']),
    ...mapActions('exercises', ['fetchExercisesForSession'])
  }
}
</script>
<style lang="css">
.container {
  max-width: 88rem;
}
</style>
