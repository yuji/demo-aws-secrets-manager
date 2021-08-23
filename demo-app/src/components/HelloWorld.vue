<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <h2>Pets</h2>
    <ul>
      <li v-for="pet in pets" :key="pet.id">
        <p><span>{{pet.type}}</span></p>
        <p><span>{{pet.price}}</span></p>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios'
import AWS from 'aws-sdk'
var region = 'us-east-1'

AWS.config.region = region
AWS.config.credentials = new AWS.CognitoIdentityCredentials({
  IdentityPoolId: 'us-east-1:0a2f0884-bdf3-49e7-93ee-05754346c345'
})

export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'Secrets Manager Demo',
      pets: [],
      api_endpoint: 'https://ltxtx28wic.execute-api.ap-northeast-1.amazonaws.com/prod/pets/'
    }
  },
  mounted () {
    var $this = this
    AWS.config.credentials.get(function (err) {
      if (err) {
        console.log(err)
      } else {
        var secretName = 'demo/api_key'
        var client = new AWS.SecretsManager({
          region: region
        })

        client.getSecretValue({SecretId: secretName}, function (err, data) {
          if (err) {
            console.log(err)
          } else {
            var secret
            if ('SecretString' in data) {
              secret = data.SecretString
            } else {
              let buff = Buffer.from(data.SecretBinary, 'base64')
              secret = buff.toString('ascii')
            }
            secret = JSON.parse(secret)
            console.log(secret)
            console.log(secret.DEMO_API_KEY)
            axios
              .get($this.api_endpoint, {
                headers: {
                  'Content-Type': 'application/json',
                  'x-api-key': secret.DEMO_API_KEY
                },
                data: {}
              })
              .then(response => {
                $this.pets = response.data
              })
          }
        })
      }
    })
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
