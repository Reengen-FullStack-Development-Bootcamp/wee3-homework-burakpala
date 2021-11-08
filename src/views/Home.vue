<template>
  <div class="home">
    <StockInfo/>
  </div>
</template>

<script>
// @ is an alias to /src
import StockInfo from '@/components/StockInfo'
export default {
  name: 'Home',
  components: {
    StockInfo
  }
}
</script>
