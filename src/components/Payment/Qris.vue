<template>
  <!-- Modal -->
  <div class="card text-center mx-auto d-block" style="width: 18rem">
    <qrcode-vue :value="value" :size="size" level="H" />
  </div>
</template>
<script lang="ts">
  import QrcodeVue from 'qrcode.vue'
export default {
    data() {
      return {
        value: 'https://example.com',
        size: 300,
      }
    },
  props: {
    data: { type: Object, required: true }
  },
  components:{
    QrcodeVue
  }
}
</script>
