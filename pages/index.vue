<template>
  <div>
    <v-row id="userform" justify="center" align="center">
      <v-card class="mx-auto" max-width="600" min-width="364" outlined>
        <div class="headline mb-1 text-center welcome">¡HOLA!</div>
        <div v-show="!success" id="cotter-form-container-form_default"></div>
        <div v-if="success" class="text-center">
          Gracias por registrarte <br />
          {{ payload.email }}
        </div>
        <div v-if="success" class="action-btn">
          <v-btn block outlined @click="verifyToken()">Verificar Token</v-btn>
        </div>
        <div v-if="success" class="action-btn">
          <v-btn block outlined @click="siginOut()">Cerrar Sesión</v-btn>
        </div>
      </v-card>
    </v-row>
    <v-snackbar v-model="snackbar">
      {{ snacktext }}
    </v-snackbar>
  </div>
</template>

<script>
import Cotter from 'cotter'
import { CotterIdentity } from 'cotter-token-js'
export default {
  data() {
    return {
      success: false,
      payload: null,
      payloadString: null,
      validToken: false,
      accessToken: null,
      snackbar: false,
      snacktext: null,
    }
  },
  mounted() {
    const cotter = new Cotter({
      // add your Cotter API Key
      ApiKeyID: '',
      ContainerID: 'cotter-form-container-form_default',
    })
    cotter
      .withFormID('form_default')
      .signInWithWebAuthnOrLink()
      .showEmailForm()
      .then((payload) => {
        this.success = true
        this.payload = payload
        this.accessToken = this.payload.oauth_token.access_token
        this.payloadString = JSON.stringify(payload, null, 4)
      })
      .catch((err) => {
        // eslint-disable-next-line no-console
        console.log(err)
      })
  },
  methods: {
    verifyToken() {
      const cotterID = new CotterIdentity(this.payload.token)
      this.validToken = cotterID.validate()
      this.snacktext = this.validToken
        ? 'El token es válido'
        : 'El token no es válido'
      this.snackbar = true
    },
    async siginOut() {
      // add your Cotter API Key
      const cotter = new Cotter('')
      await cotter.logOut()
      this.success = false
      this.payload = null
      this.accessToken = null
      this.payloadString = null
      this.validToken = false
      this.snacktext = null
      location.reload()
    },
  },
}
</script>
<style scoped>
#userform {
  margin-top: 50px;
}
.welcome {
  padding: 1.5rem 2rem;
}
#cotter-form-container-form_default {
  padding: 1.5rem 2rem;
}
.action-btn {
  padding: 0.2rem;
}
</style>
