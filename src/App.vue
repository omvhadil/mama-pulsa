<script setup>
import { computed, reactive, ref, watchEffect } from "vue";

// 
const mama = reactive({
  data: JSON.parse(localStorage.getItem("mamapulsa") || "[]"),
  provider: null,
  nomorHp: null,
  nominal: null,
  catatan: null,
  selected: null,
  mode: "add",
  search: null,
});

watchEffect(() => {
  localStorage.setItem("mamapulsa", JSON.stringify(mama.data));
  console.log(mama.data);
});

function createTodo() {
  if (mama.mode === "add") {
    mama.data.unshift({
      id: Math.random(),
      tanggal: new Date().toISOString(),
      provider: mama.provider,
      nomorHp: mama.nomorHp,
      nominal: mama.nominal,
      catatan: mama.catatan,
    });
    mama.provider = null; mama.nomorHp = null; mama.nominal = null; mama.catatan = null;
  } else {
    const i = mama.data.findIndex((o) => o.id === mama.selected.id);
    mama.data[i] = {
      provider: mama.provider,
      nomorHp: mama.nomorHp,
      nominal: mama.nominal,
      catatan: mama.catatan,
    }
    // Reset
    mama.selected = {};
    mama.provider = null;
    mama.nomorHp = null;
    mama.nominal = null;
    mama.catatan = null;
    mama.mode = "add";
  }
};
const filteredMama = computed(() => {
  return mama.data;
});
function delMama(idx) {
  mama.data.splice(idx, 1);
};

</script>

<template>
  <div class="container">
    <h1>Mama Pulsa</h1>
    <h4 class="mb-4">Mama Pulsa</h4>

    <div class="row">
      <!-- =================== colom inputan =============== -->
      <div class="col-3 border rounded p-2">
        <h5 class="mt-3">📲 Beli Pulsa</h5>
        <hr>
        <form @submit.prevent="createTodo">
          <select class="form-select mb-3" v-model="mama.provider" aria-label="Default select example">
            <option :value="null">Pilih Provider</option>
            <option value="TELKOMSEL">TELKOMSEL</option>
            <option value="XL">XL</option>
            <option value="IM3">IM3</option>
          </select>
          <h6>Nomor Hp</h6>
          <input class="form-control mb-3" v-model="mama.nomorHp" placeholder="Masukkan Nomor. HP" type="text"
            aria-label="default input example">
          <h6>Nominal</h6>
          <input class="form-control mb-3" v-model="mama.nominal" placeholder="Masukkan Nominal Pulsa" type="text"
            aria-label="default input example">
          <h6>Catatan</h6>
          <textarea class="form-control mb-3" v-model="mama.catatan" placeholder="Masukkan Catatan"
            id="exampleFormControlTextarea1" rows="3"></textarea>
          <button type="submit" class="btn btn-primary">{{ mama.mode === "add" ? "Simpan" : "Edit" }}</button>
        </form>
      </div>

      <!-- ============== colom data =================== -->
      <div class="col-8 ms-4">
        <div class="row">
          <div class="col">
            <input type="text" class="form-control" placeholder="Cari" aria-label="First name">
          </div>
          <div class="col">
            <select class="form-select mb-3" aria-label="Default select example">
              <option selected>Filter by Provider</option>
              <option value="1">TELKOMSEL</option>
              <option value="2">XL</option>
              <option value="3">IM3</option>
            </select>
          </div>
          <div class="col">
            <select class="form-select mb-3" aria-label="Default select example">
              <option selected>Filter by Status</option>
              <option value="1">PENDING</option>
              <option value="2">SUCCESS</option>
              <option value="3">CANCELED</option>
            </select>
          </div>
        </div>
        <table class="table border-top">
          <thead>
            <tr>
              <th scope="col">DATE</th>
              <th scope="col">PROVIDER</th>
              <th scope="col">NOMOR HP</th>
              <th scope="col">NOMINAL</th>
              <th scope="col">STATUS</th>
              <th scope="col">CATATAN</th>
              <th scope="col">ACTION</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in filteredMama" :key="item.id">
              <td>{{ item.tanggal }}</td>
              <td>{{ item.provider }}</td>
              <td>{{ item.nomorHp }}</td>
              <td>Rp. {{ item.nominal }}</td>
              <td>{{ item.status }}</td>
              <td>{{ item.catatan }}</td>
              <td>
                <button type="button" @click="
                    mama.mode = 'edit';
                    mama.provider = item.provider;
                    mama.nomorHp = item.nomorHp;
                    mama.nominal = item.nominal;
                    mama.catatan = item.catatan;
                    mama.selected = item;
                " class="btn btn-success mx-1">✍</button>
                <button type="button" @click="delMama(index)" class="btn btn-danger">❌</button>
              </td>
            </tr>
          </tbody>
        </table>

      </div>
    </div>
  </div>
</template>

<style>
* {
  font-size: .9rem;
}

.container {
  margin-top: 1rem;
}
</style>
