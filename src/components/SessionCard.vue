<template>
  <v-card
    class="ma-2 session"
    height="8em"
    width="16em"
    :class="{
      grey: !isSessionValidated,
      'darken-3': !isSessionValidated,
      'green': isSessionValidated,
      'darken-2': isSessionValidated
    }"
    tile
    outlined
    >
    <div class="d-flex flex-no-wrap justify-space-between">
      <div>
        <v-card-title class="subtitle-1">
          {{ session.name }}
        </v-card-title>
      </div>
      <div>
        <v-card-text>
          <div class="d-flex flex-column flex-no-wrap justify-space-between">
            <div>
              {{ getExercisesBySessionId(session.id).length }}
              <v-tooltip bottom>
                <template v-slot:activator="{ on }">
                  <v-icon v-on="on">mdi-hammer</v-icon>
                </template>
                <span>Nombre d'exercices</span>
              </v-tooltip>
            </div>
            <div style="text-align: right; padding-top: 1em">
              <v-tooltip bottom >
                <template v-slot:activator="{ on }">
                  <router-link :to="`/session/${session.id}/edit/${firstExerciseId}`">
                    <v-icon v-on="on">mdi-pencil</v-icon>
                  </router-link>
                </template>
                <span>Modifier l'exercice</span>
              </v-tooltip>
            </div>
          </div>
        </v-card-text>
      </div>
    </div>
  </v-card>
</template>
<script>
import { mapGetters } from 'vuex'
export default {
  props: {
    session: Object
  },
  computed: {
    ...mapGetters(['hasAccessRight']),
    ...mapGetters('exercises', ['getExercisesBySessionId']),
    firstExerciseId () {
      if (this.exercises.length) {
        return this.exercises[0].id
      } else {
        return 0
      }
    },
    exercises () {
      if (!this.session) return []
      return this.getExercisesBySessionId(this.session.id)
    },
    isSessionValidated () {
      if (!this.session) return false
      if (this.exercises.length) {
        return this.exercises.every(e => e.valid)
      } else {
        return false
      }
    }
  },
  methods: {
    s (text) {
      const max = 17
      return text.length > max ? `${text.slice(0, max)}…` : text
    }
  }
}
</script>
<style lang="css">
.session {
  border-radius: 8px;
}
</style>
