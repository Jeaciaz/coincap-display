<template>
  <div id="app">
    <nav class="navbar">
      <a href="/" class="navbar__header">
        <img src="../assets/logo.png" alt="CoinCap" class="navbar__logo">
        <span>CoinCap Display</span>
      </a>
    </nav>
    
    <CurrencyTable/>
  </div>
</template>
 
<script>
import CurrencyTable from "./CurrencyTable.vue";
 
export default {
  name: "app",
  components: {
    CurrencyTable
  }
};
</script>
 
<style lang="scss">
@import url('https://fonts.googleapis.com/css?family=Raleway:700|Roboto&display=swap');
 
$color-primary-900: hsl(159, 70, 19);
$color-primary-700: hsl(159, 70, 39);
$color-primary-500: hsl(159, 70, 62);
$color-primary-300: hsl(159, 70, 77);
$color-primary-100: hsl(159, 70, 92);
 
$color-text: #333;
 
body {
  font-family: 'Roboto', Arial, sans-serif;
  max-width: 1200px;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.1);
  min-height: 100vh;
  margin: auto;
  color: $color-text;
}
 
.navbar {
  border-radius: 0 0 5px 5px;
  background: $color-primary-500;
  box-shadow: 0 3px 3px rgba(0, 0, 0, 0.1);
 
  &__logo {
    display: inline-block;
    max-height: 50px;
    padding: 1rem;
    padding-left: 3rem;
    vertical-align: middle;
  }
 
  &__header {
    text-decoration: none;
    color: $color-text;
    font-family: 'Raleway', Arial, sans-serif;
    font-size: 1.5rem;
    line-height: 50px;
    padding: 1rem 0;
    vertical-align: middle;
  }
}
 
</style>
 