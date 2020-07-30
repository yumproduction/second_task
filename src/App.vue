<template>
  <div id="app">
    <SimonGame/>
  </div>
</template>

<script>
import SimonGame from './components/SimonGame.vue'

export default {
  name: 'App',
  components: {
    SimonGame
  }
}
</script>

<style lang = 'scss'>
*{
  margin: 0;
  padding: 0;
}

$maxWidth: 1280;

@mixin fontSize($pcSize, $mobSize) {
 $addSize: $pcSize - $mobSize;
 $maxWidth: $maxWidth - 320;
 font-size: calc(#{$mobSize + px} + #{$addSize} * ((100vw - 320px) / #{$maxWidth}));
}

body{
    @include fontSize(16, 14);
    -webkit-font-smoothing: antialaised;
    -webkit-overflow-scrolling: touch;
}

h2{
    @include fontSize(34, 22);
    font-weight: 500;
}

p{
    @include fontSize(18, 16);
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

</style>
