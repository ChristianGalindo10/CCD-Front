<template>
  <div>
    <Tutorial />
    <div style="padding: 10px">
      <h1>Nuxt.js + Spring Boot</h1>
      <p>Message from API: {{ ip }}</p>
    </div>
  </div>
</template>

<script>
/**export default {
  name: 'IndexPage'
}**/
export default {
  //#layout: 'defecto',
  async asyncData({ app }) {
    const ip = await app.$axios.$get('http://localhost:8080/usuarios/get');
    console.log(ip);
    return { ip }
  }
}
</script>
