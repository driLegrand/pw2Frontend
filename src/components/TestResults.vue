<template>
  <v-list v-if="results.tests">
    <v-list-item
      v-for="test of results.tests" :key="test.name"
      class="test-item"
      :class="{red: !!test.failure, green: !test.failure && !test.placeholder,
      grey: test.placeholder, 'darken-4': !test.placeholder, 'darken-3':
      test.placeholder}">
      <v-list-item-icon>
        <v-icon v-if="test.failure">mdi-alert-circle</v-icon>
        <v-icon v-else-if="!test.placeholder">mdi-check</v-icon>
        <v-icon v-else>mdi-minus</v-icon>
      </v-list-item-icon>
      <v-list-item-content>
        <v-list-item-title>
          <div v-if="test.failure">
            <p style="white-space: pre-wrap">{{ test.failure.message }}</p>
          </div>
          {{ test.name }}{{ test.time ? ` - ${test.time} ms` : '' }}
        </v-list-item-title>
        <v-list-item-subtitle v-if="test.failure && test.expand">
          <pre style="white-space: pre-wrap">{{ test.failure.stacktrace }}</pre>
        </v-list-item-subtitle>
      </v-list-item-content>
      <v-list-item-action v-if="test.failure">
        <v-btn icon @click="toggle(test)">
          <v-icon v-if="test.expand">mdi-chevron-up</v-icon>
          <v-icon v-else>mdi-chevron-down</v-icon>
        </v-btn>
      </v-list-item-action>
    </v-list-item>
  </v-list>
</template>

<script>
/**
 * @typedef {Object} TestResult
 * @prop {Boolean} [placeholder]
 * @prop {String} file
 * @prop {Number} line
 * @prop {String} name
 * @prop {Number} time
 * @prop {Object} failure
 * @prop {String} failure.stacktrace
 * @prop {String} failure.message
 */

/**
 * @typedef {Object} TestSuite
 * @prop {Object} stats
 * @prop {Number} stats.errors
 * @prop {Number} stats.failures
 * @prop {Number} stats.skipped
 * @prop {Number} stats.tests
 * @prop {Number} stats.time
 * @prop {String} stats.timestamp
 * @prop {Array<TestResult>} tests
 */

/**
 * @typedef {Object} APIResult
 * @prop {String} stdout
 * @prop {String} stderr
 * @prop {TestSuite} result
 */
export default {
  props: {
    results: Object
  },
  watch: {
    results () {
      for (let i = 0; i < this.results.tests.length; i++) {
        this.$set(this.results.tests[i], 'expand', false)
      }
      this.results.tests.sort((a, b) => a.failure ? b.failure ? 0 : -1 : 1)
    }
  },
  methods: {
    toggle (test) {
      test.expand = !test.expand
      // this.$forceUpdate()
    }
  }
}
</script>
<style lang="css">
.test-item {
  margin-bottom: 0.2em;
  border-radius: 8px;
}
</style>
