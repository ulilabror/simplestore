<template>
  <!-- Modal -->
  <div
  ref="modal"
    class="modal fade show"
    id="exampleModal"
    tabindex="-1"
    aria-labelledby="exampleModalLabel"
    aria-hidden="true"
    :style="{'display':display}"
  >
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h1 class="modal-title fs-5" id="exampleModalLabel">Pembayaran</h1>
          <button
            type="button"
            class="btn-close"
            data-bs-dismiss="modal"
            aria-label="Close"
            @click="closeModal"
          ></button>
        </div>
        <div class="modal-body">
            <div v-if="data.payment.status == 1">
                <Va
                  :data="data.payment.briva"
                />
            </div>
            <div v-if="data.payment.status == 2">
                <Qris :data="data.payment.qris" />
            </div>

        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-danger" @click="closeModal" data-bs-dismiss="modal" data-bs-target="#exampleModal" >Tutup</button>
          <button type="button" class="btn btn-primary">Unduh Invoice</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import Va from './Payment/Va.vue'
import Qris from './Payment/Qris.vue';
export default {

  methods: {
    closeModal(){
      this.$emit("closePaymentModal")
    }
  },
  props: {
    data: { type: Object, required: true },display: String
  },components:{
    Va,
    Qris
  }
}
</script>
