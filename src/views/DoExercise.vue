<template>
  <v-row cols="14">
    <v-col cols="2">
      <v-row class="flex-column">
        <h1 class="pa-2">{{ sessionName }}</h1>
        <router-link :to="`/session/${sessionId}/do/${exercise.id}`"
          style="color: white; text-decoration: none"
          v-for="exercise of getExercisesBySessionId(sessionId)"
          :key="exercise.id">
          <ExerciseCard :exercise="exercise" height="6rem" :width="null"
                        :session="{id: sessionId}"
                        :current="exercise.id === exerciseId"></ExerciseCard>
        </router-link>
      </v-row>
    </v-col>
    <v-col cols="10">
      <ExerciseStudent
        :exercise-id="exerciseId"
        :session-id="sessionId"
      ></ExerciseStudent>
    </v-col>
  </v-row>
</template>
<script>
import ExerciseCard from '../components/ExerciseCard.vue'
import ExerciseStudent from '../components/ExerciseStudent.vue'
import { mapActions, mapGetters } from 'vuex'

export default {
  components: {
    ExerciseStudent,
    ExerciseCard
  },
  data: () => ({
    sessionId: 0,
    exerciseId: 0
  }),
  computed: {
    ...mapGetters('sessions', ['getSessionById']),
    ...mapGetters('exercises', ['getExercisesBySessionId']),
    sessionName () {
      const sess = this.getSessionById(this.sessionId)
      if (sess) return sess.name
      return 'Loading...'
    }
  },
  watch: {
    $route (to, from) {
      if (this.sessionId !== parseInt(to.params.sessionId)) {
        this.fetchSession({ id: parseInt(to.params.sessionId) })
      }
      this.sessionId = parseInt(to.params.sessionId)
      this.exerciseId = parseInt(to.params.exerciseId)
    }
  },
  methods: {
    ...mapActions('sessions', ['fetchSession']),
    ...mapActions('exercises', ['fetchExercisesForSession'])
  },
  async mounted () {
    this.sessionId = parseInt(this.$route.params.sessionId)
    this.exerciseId = parseInt(this.$route.params.exerciseId)
    if (!isNaN(this.sessionId) && this.sessionId !== 0 && this.exerciseId !== 0) {
      await this.fetchSession({ id: this.sessionId })
      await this.fetchExercisesForSession({ sessionId: this.sessionId })
    }
  }
}
</script>
