<template>
  <!-- Modal -->
  <div class="modal fade show " id="invoices" tabindex="-1" aria-labelledby="invoices" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h1 class="modal-title fs-5" id="exampleModalLabel">{{ data.title }}</h1>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body" style="overflow: scroll ;">
          <table class="table ">
            <thead>
              <tr>
                <th scope="col">No</th>
                <th scope="col">Nomer</th>
                <th scope="col">Nama</th>
                <th scope="col">Trxid</th>
                <th scope="col">Waktu</th>
                <th scope="col">Status</th>
                <th scope="col">Harga</th>
                <th scope="col">Harga total</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(o, index) in data.invoices" :key="index">
                <th scope="row">{{ index + 1 }}</th>
                <td>{{ o.nomer ? o.nomer : "Tidak ditemukan" }}</td>
                <td>{{ o.nama ? o.nama : "Customer" }}</td>
                <td>{{ o.trxid ? o.trxid : "tidak ditemukan" }}</td>
                <td>{{ o.waktu ? o.waktu : "tidak ditemukan" }}</td>
                <td>{{ o.status ? o.status : "tidak ditemukan" }}</td>
                <td>{{ o.harga ? o.harga : "tidak ditemukan" }}</td>
                <td>{{ accumulateHarga(index) }}</td>
              </tr>
            </tbody>
          </table>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-danger" data-bs-dismiss="modal">Tutup</button>
          <button type="button" @click="clear" class="btn btn-danger" data-bs-dismiss="modal">Clear</button>
          <button type="button" class="btn btn-primary">Unduh Invoice</button>
        </div>
      </div>
    </div>
  </div>
</template>
  
<script lang="ts">
export default {
  methods: {
    clear() {
      localStorage.setItem("data", '[]')
      this.data.invoices = []
    },
    accumulateHarga(index: number) {
      return this.cumulativeHarga[index];
    }
  },computed: {
    cumulativeHarga() {
      let cumulativeSum = 0;
      return this.data.invoices.map((o: any) => {
        if (o.harga) {
          cumulativeSum += o.harga;
        }
        return cumulativeSum;
      });
    }
  },
  props: {
    data: { type: Object, required: true }
  }
};
</script>
  