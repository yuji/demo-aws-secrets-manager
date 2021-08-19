<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <h2>Pets</h2>
    <ul>
      <li v-for="pet in pets" :key="pet.id">
        <p><span>{{type}}</span></p>
        <p><span>{{price}}</span></p>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'Secrets Manager Demo',
      pets: [],
      api_endpoint: 'https://ltxtx28wic.execute-api.ap-northeast-1.amazonaws.com/prod/pets/',
      api_key: '4goMJWKP3rK75P6qb8sn9ARsCec2mkT96ig6iS50'
    }
  },
  mounted () {
    this.$axios
      .get(this.api_endpoint, {
        headers: {
          'Content-Type': 'application/json',
          'x-api-key': this.api_key
        },
        data: {}
      })
      .then(response => {
        this.pets = response.data.contents
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
