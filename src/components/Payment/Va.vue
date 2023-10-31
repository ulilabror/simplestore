<template>
  <!-- Modal -->
  <div class="card text-center">
    <table class="table">
      <tbody>
        <tr>
          <th scope="row">Account Number</th>
          <td>:</td>
          <td>{{ data.nomer }}</td>
        </tr>
        <tr>
          <th scope="row">Account Holder</th>
          <td>:</td>
          <td>{{ data.nama }}</td>
        </tr>
        <tr>
          <th scope="row">Bank Code</th>
          <td>:</td>
          <td>{{ data.bankCode }}</td>
        </tr>
        <tr>
          <th scope="row">Account Type</th>
          <td>:</td>
          <td>{{ data.accountType }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
<script lang="ts">
export default {
  props: {
    data: { type: Object, required: true }
  }
}
</script>
