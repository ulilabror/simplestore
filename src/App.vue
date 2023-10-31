<template>
  <div class="container mt-3 mb-3 d-flex justify-content-center">
    <div class="card px-1 py-1">
      <div class="card-body">
        <figure class="text-center">
          <blockquote class="blockquote">
            <h1>CASINI STORE</h1>
          </blockquote>
          <figcaption class="blockquote-footer">
            cepat, mudah, dan murah hanya di
            <cite title="Source Title">https://store.carisini.my.id</cite>
          </figcaption>
        </figure>

        <h6 class="card-title mt-4 mb-3" v-if="pickedlist">
          Apa yang ingin anda beli!<br />
        </h6>

        <div class="row d-flex">
          <div v-for="(item, index) in packageList" :key="index" class="col-6">
            <PackageList :package="item" @pickedlist="pickedlist = $event"></PackageList>
          </div>
        </div>
        <transition>
          <div class="form" v-if="pickedlist">
            <h6 class="card-title mt-4 mb-3">
              Silahkan pilih paket!<br />
            </h6>
            <div class="input-group mb-3">
              <div class="input-group-prepend col-2">
                <label class="input-group-text" for="inputGroupSelect01">select</label>
              </div>
              <select class="custom-select col-10" id="inputGroupSelect01" v-model="formData.code">
                <option selected disabled>Pilih Mana?...</option>
                <option v-for="{ Code, Name, Price } in packages" :value="Code">{{ Name }}, {{ formatCurrency(Price) }}
                </option>
              </select>
            </div>
          </div>
        </transition>

        <h6 class="information mt-4" v-if="pickedlist">
          Pastikan data disi dengan tepat !
        </h6>

        <transition>

          <div class="row mb-3" v-if="pickedlist && forms">
            <div :class="{ 'col-12 mb-2': pascabayar === null, 'col-7 mb-2': pascabayar === 1 }">
              <div v-for="(form, index) in forms" :key="index">
                <div style="margin-bottom: 4px;" v-html="form.Html" @change="getValues" ref="no"></div>
              </div>
            </div>
            <div v-if="pascabayar === 1" class="col-5">
              <button ref="periksa" style="height: 100%;" type="button" data-bs-toggle="modal"
                data-bs-target="#exampleModal" @click="cektagihan"
                class="btn btn-primary btn-block btn-sm confirm-button">Periksa
                Tagihan</button>
              <ModalInvoice :data="data" />
            </div>
          </div>
        </transition>

        <transition>
          <div id="bayar" v-if="forms[0].Type != ''">
            <h6 class="information mt-4">Metode Pembayaran</h6>
            <div class="row d-flex">
              <label class="radio col">
                <input type="radio" name="add" v-model="data.payment.status" value="2" />

                <span> <i class="fa fa-user"></i> Qris </span>
              </label>
              <label class="radio col">
                <input type="radio" name="add" v-model="data.payment.status" value="1" />
                <span> <i class="fa fa-user"></i> BRI VA </span>
              </label>
              <label class="radio col">
                <input type="radio" name="add" value="anz" checked />
                <span> <i class="fa fa-user"></i> Cashless </span>
              </label>
            </div>
            <div class="d-grid gap-2 row-6 mt-4 mx-auto">
              <button id="payment" @click="submit" type="button" class="btn btn-success btn-block confirm-button">
                Beli Sekarang
              </button>
              <button id="history" type="button" data-bs-toggle="modal" data-bs-target="#invoices"
                class="btn btn-primary btn-block confirm-button">
                Riwayat Transaksi
                {{ data.payment }}
              </button>

              <!-- Button trigger modal -->
              <ModalInvoice ref="invoices" :data="data" />
              <ModalPayment :data="data" @closePaymentModal="closePaymentModal" :display="displayPayment" ref="payment" />
            </div>


          </div>

        </transition>
        <div class="d-flex flex-column text-center px-5 mt-3 mb-3">
          <p class="agree-text">Dengan membeli di toko kami, Anda wajib mematuhi s&k di toko kami</p>
          <a href="#" class="terms">Syarat & Ketentuan</a>
        </div>
      </div>
    </div>
    <!-- Option 1: Bootstrap Bundle with Popper -->
    <component :is="'script'" src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta3/dist/js/bootstrap.bundle.min.js"
      integrity="sha384-JEW9xMcG8R+pH31jmWH6WWP0WintQrMb4s7ZOdauHnUtxwoG2vI5DkLtS3qm9Ekf" crossorigin="anonymous">
    </component>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import PackageList from './PackageList.vue';
import ModalInvoice from './components/ModalInvoice.vue';
import ModalPayment from './components/ModalPayment.vue';

const API_URL = "http://localhost/"
// const API_URL = "https://store.carisini.my.id/"

export default defineComponent({
  data() {
    return {
      data: { title: "tagihan", content: "no content avaiable", success: true, invoices: [] as any, payment: { status: 0, qris: {}, briva: {} } },
      formData: { code: '', no: '' },
      displayPayment: "none"
      ,
      picked: null,
      pascabayar: 0,
      pickedlist: null,
      forms: [{ Type: "", Html: "" }],
      packageList: [{ CodePkgs: '', NamePkg: '' }],
      packages: [{ Name: 'kosong', Code: '0', Price: "0" }],
      payment: { method: "voucher", value: "08773216" }
    };
  },
  mounted() {
    try {

      this.data.invoices = localStorage.getItem("data") ? JSON.parse(localStorage.getItem("data") as string) : []
      console.log(

        JSON.parse(localStorage.getItem("data") as string)
      );
    } catch (err) {
      alert("error")
      return
    }
  },
  methods: {
    updateStatusByTrxId(trxid: any) {
    // Find the payment with the given trxid
    const payment = this.data.invoices.find((p: any) => p.trxid === trxid);

    // If payment is found, update the status
    if (payment) {
      payment.status = 'paid';
      // If you want to notify about the update or perform any other action, you can do it here
    } else {
      // If no payment is found, you can handle this case here
      payment.status = 'paid';
    }
  },
    closePaymentModal() {
      this.displayPayment = "none"
    },
    save(json: any) {
      const objData = { nama: json.data.customer_name, nomer: json.data.customer_no, trxid: json.data.ref_id, harga: json.data.price, waktu: json.data.waktu, status: json.data.status }
      this.data.invoices.push(objData)
      localStorage.setItem('data', JSON.stringify(this.data.invoices));
    },
    submit() {
      if (this.validateform() === 0) {
        // Perform submit logic

        this.purchase().then(() => {
          const s = fetch(`${API_URL}rst`, { method: 'GET', credentials: 'include' });

          this.getrx()
        }).then(() => { this.displayPayment = "block"; }).catch((error) => {
          // Handle the error from the purchase() method here
          console.error("An error occurred in the purchase() method:", error);
        });
      }
    },
    cektagihan() {
      if (this.validateform() === 0) {
        this.invoice().then(() => this.$refs.periksa)
      }
      console.log(this.formData);
    },
    async getrx() {
      const optionsGet: RequestInit = {
        method: 'GET',
        credentials: 'include',
      };
      const getrx = await fetch(`${API_URL}getrx`, optionsGet);
      if (!getrx.ok) alert("Maaf ada kesalahan silahkan hubungi develover"); return
    }
    ,
    async purchase() {
      try {
        const options: RequestInit = {
          method: 'POST',
          credentials: 'include',
          headers: {
            "Content-Type": "application/x-www-form-urlencoded",
          },
          body: `code=${this.formData.code}&no=${this.formData.no}&pb=${this.pascabayar}&tp=${this.payment.method}&pw=${this.payment.value}`,
        };

        const url = `${API_URL}payment`;
        // await (await fetch(url, options)).json();
        const json = { data: { customer_name: "setia", customer_no: "087732168347", ref_id: "HIODJBJKKB83", price: 5000, status: "success", waktu: "3803820832" }, }

        this.save(json)
        return true

      } catch (err) {
        alert("maaf tidak bisa mengambil data dari server. Mohon muat ulang halaman...")
        throw err

      }
    }
    ,
    async invoice() {
      try {

        const options: RequestInit = {
          method: 'POST',
          credentials: 'include',
          headers: {
            "Content-Type": "application/x-www-form-urlencoded",
          },
          body: `code=${this.formData.code}&no=${this.formData.no}`,
        };
        const url = `${API_URL}invoice`;
        const json = await (await fetch(url, options)).json();
        this.save(json)


      } catch (err) {
        alert("maaf tidak bisa mengambil data dari server. Mohon muat ulang halaman...")
        return
      }
    },
    async fetchMenu() {
      try {

        const options = {
          method: 'GET'
        };
        const url = `${API_URL}menu`;
        const json = await (await fetch(url, options)).json();
        this.packageList = json.data[0]


      } catch (err) {
        alert("maaf tidak bisa mengambil data dari server. Mohon muat ulang halaman...")
        return
      }
      // console.log(this.packageList);
    },
    async fetchPackages(type: string) {
      try {

        const options = {
          method: 'POST',
          headers: {
            "Content-Type": "application/x-www-form-urlencoded",
          },
          body: `type=${type}`,
        };
        const url = `${API_URL}packages`;
        const json = await (await fetch(url, options)).json();
        this.packages = json.data[0]


      } catch (err) {
        alert("tidak dapat mengambil data " + type)
        return

      }
    },
    async fetchForms(type: string) {
      try {

        const options = {
          method: 'POST',
          headers: {
            "Content-Type": "application/x-www-form-urlencoded",
          },
          body: `type=${type}`,
        };
        const url = `${API_URL}forms`;
        const json = await (await fetch(url, options)).json();
        this.forms = json.data[0]


      } catch (err) {
        console.log(err);
        return


      }
    },
    updatePascabayar() {
      if (this.$refs.no) {
        let hasPascabayar = false;
        const noRefs = this.$refs.no as (HTMLElement | Element | undefined)[];
        for (let i = 0; i < noRefs.length; i++) {
          let tagDiv = noRefs[i] as HTMLElement;
          if (tagDiv) {
            let doc = document.createElement('div');
            doc.innerHTML = tagDiv.innerHTML;
            let pascabayarInput = doc.querySelector('input[pascabayar]');
            if (pascabayarInput) {
              hasPascabayar = true;
              break;
            }
          }
        }
        this.pascabayar = hasPascabayar ? 1 : 0;
      }
    }
    ,
    getValues() {
      let no = '';
      const noRefs = this.$refs.no as (HTMLElement | Element | undefined)[];
      for (let i = 0; i < noRefs.length; i++) {
        let tagDiv = noRefs[i] as HTMLElement;
        if (tagDiv) {
          let inputElement = noRefs[i]?.querySelector('input');
          if (inputElement) {
            no += inputElement.value;
          }
        }
      }
      this.formData.no = no;
    },
    formatCurrency(amounts: string, locale = 'id-ID', currency = 'IDR') {
      let amount: number = +amounts
      return amount.toLocaleString(locale, { style: 'currency', currency });
    },
    validateform() {
      if (this.formData.code.trim() == "") {
        alert("Pastikan Anda telah memilih paket yang akan dibeli...")
        return 1
      }
      if (this.formData.no.trim() == "") {
        alert("Pastikan Anda telah mengisi data dengan benar...")
        return 1
      }
      return 0
    },
    e(packageCode: string) {
      var pulsaOnly = packageCode.substring(0, packageCode.indexOf("."));
      return pulsaOnly;
    }
  }, beforeMount() {
    // fetch on init
    this.fetchMenu();
    console.log("created")
  }, watch: {
    pickedlist(n, o) {
      this.fetchPackages(n)
      this.fetchForms(this.e(n)).then(() => this.updatePascabayar())
    },
    data(n) {
      localStorage.data = n
      console.log(n)
    },
  },
  components: {
    PackageList,
    ModalInvoice,
    ModalPayment
  },
});
</script>

<style scoped>
.v-enter-active,
.v-leave-active {
  transition: opacity 0.5s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}

.body {
  background-color: #ffebee;
}

.card {
  width: 400px;
  background-color: #fff;
  border: none;
  border-radius: 12px;
}

label.radio {
  cursor: pointer;
  width: 100%;
}

label.radio input {
  position: absolute;
  top: 0;
  left: 0;
  visibility: hidden;
  pointer-events: none;
}

label.radio span {
  padding: 7px 14px;
  border: 2px solid #eee;
  display: inline-block;
  color: #039be5;
  border-radius: 10px;
  width: 100%;
  height: 48px;
  line-height: 27px;
}

label.radio input:checked+span {
  border-color: #039be5;
  background-color: #81d4fa;
  color: #fff;
  border-radius: 9px;
  height: 48px;
  line-height: 27px;
}

.button.clicked {
  background-color: #81d4fa;
  ;
}

.form-control {
  margin-top: 10px;
  height: 48px;
  border: 2px solid #eee;
  border-radius: 10px;
}

.form-control:focus {
  box-shadow: none;
  border: 2px solid #039be5;
}

.agree-text {
  font-size: 12px;
}

.terms {
  font-size: 12px;
  text-decoration: none;
  color: #039be5;
}

.confirm-button {
  height: 50px;
  border-radius: 10px;
}
</style>
