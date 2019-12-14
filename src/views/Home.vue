<template>
  <div class="home">
    <!-- <img alt="Vue logo" src="../assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    <test></test>
  </div>
</template>

<script>

// @ is an alias to /src
import test from '@/components/test'

export default {
  name: 'home',
  components: {
    test
  }
}
</script>
