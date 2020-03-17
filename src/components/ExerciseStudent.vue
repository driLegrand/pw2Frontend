<template>
  <v-layout column justify-center align-center>
    <v-flex xs12 sm8 md12 class="container">
      <v-card>
        <v-card-text>
          <v-row no-gutters>
            <v-col cols="6" class="pa-4 flex-grow-1">
              <h2 class="display-1">{{ exercise.title }}</h2>
              <InstructionsEditor :editable="false" :content="exercise.instructions">
              </InstructionsEditor>
              <h2 class="display-1">Résultats des tests</h2>
              <TestResults :results="testsOutput"></TestResults>
            </v-col>
            <v-col cols="6" class="pa-4 flex-grow-1">

              <h2 class="display-1" style="margin-bottom: 0.5em">
                Votre solution
                <v-tooltip bottom>
                  <template v-slot:activator="{ on }">
                    <v-btn color="primary" v-on="on" @click="execute">
                      <v-icon>mdi-play</v-icon>
                    </v-btn>
                  </template>
                  <span>Lancer</span>
                </v-tooltip>
              </h2>
              <CodeEditor
                name="solution"
                :lang="exercise.lang"
                :content="solution"
                @change="setSolution"
                :editor-height="`35rem`">
              </CodeEditor>
            </v-col>
          </v-row>
        </v-card-text>
      </v-card>
    </v-flex>
  </v-layout>
</template>
<script>
import { mapState, mapGetters, mapActions, mapMutations } from 'vuex'
import InstructionsEditor from '../components/InstructionsEditor.vue'
import CodeEditor from '../components/CodeEditor.vue'
import TestResults from '../components/TestResults.vue'

export default {
  components: {
    InstructionsEditor,
    CodeEditor,
    TestResults
  },
  props: {
    exerciseId: {
      type: Number,
      required: true
    },
    sessionId: {
      type: Number,
      required: true
    }
  },
  data: () => ({
    templateRegions: [],
    solution: '',
    regions: []
  }),
  computed: {
    ...mapGetters('exercises', ['getExerciseById']),
    ...mapGetters('attempts', ['getLastAttemptForExercise']),
    ...mapState('attempts', ['lastAttemptResults']),
    exercise () {
      return this.getExerciseById(this.exerciseId) || {}
    },
    lastAttempt () {
      return this.getLastAttemptForExercise(this.exerciseId) || {}
    },
    testsOutput () {
      if (!this.lastAttemptResults || !this.lastAttemptResults.tests) {
        if (!this.exercise.test_names) {
          return { tests: [] }
        }
        return {
          tests: this.exercise.test_names.map(t => ({
            placeholder: true,
            name: t
          }))
        }
      } else {
        return this.lastAttemptResults
      }
    }
  },
  watch: {
    exerciseId (newVal) {
      if (typeof newVal === 'number' && !isNaN(newVal)) {
        this.updateView()
      }
    }
  },
  async mounted () {
    await this.updateView()
  },
  methods: {
    ...mapMutations('exercises', ['updateValidatedExercise']),
    ...mapActions('exercises', ['fetchExerciseForSession']),
    ...mapActions('attempts', ['createAttemptForSession',
      'fetchLastAttemptForExercise']),
    async updateView () {
      await Promise.all([
        this.fetchExerciseForSession({
          sessionId: this.sessionId,
          exerciseId: this.exerciseId
        }),
        this.fetchLastAttemptForExercise({
          sessionId: this.sessionId,
          exerciseId: this.exerciseId
        })
      ])
      this.setDefaultSolution()
    },
    setDefaultSolution () {
      if (this.lastAttempt.solution) {
        this.solution = this.lastAttempt.solution
      } else {
        if (this.exercise.template_regions && this.exercise.template_regions.length) {
          this.solution = this.exercise.template_regions.join('\n')
        } else {
          this.solution = ''
        }
      }
    },
    async execute () {
      await this.createAttemptForSession({
        exerciseId: this.exerciseId,
        sessionId: this.sessionId,
        solution: this.solution
      })
    },
    setSolution ({ text, regions }) {
      this.solution = text || ''
    }
  }
}
</script>
