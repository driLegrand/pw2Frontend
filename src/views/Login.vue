<template>
  <v-container fluid>
    <v-row justify="center">
      <v-col cols="3">
        <v-text-field v-model="email" label="Votre email">
        </v-text-field>
        <v-text-field v-model="password" type="password" label="Votre mot de passe">
        </v-text-field>
        <v-btn @click="triggerLogin">Se connecter</v-btn>
      </v-col>
    </v-row>
  </v-container>
</template>
<script>
import { mapActions } from 'vuex'

export default {
  data: () => ({
    email: '',
    password: ''
  }),
  methods: {
    ...mapActions('user', ['login']),
    async triggerLogin () {
      try {
        await this.login({
          email: this.email,
          password: this.password
        })
        this.$router.push('/')
      } catch (err) {
        console.log('oups')
      }
    }
  }
}
</script>
