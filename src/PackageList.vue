<template>
    <label class="form-check-label">
      <input
        type="radio"
        name="type"
        :value="package.CodePkgs"
        v-model="pickedlist"
        @click="$emit('pickedlist', package.CodePkgs)"
        class="form-check-input"
      />
      {{ package.NamePkg }}
    </label>
  </template>
  
  <script lang="ts">
  import { defineComponent, type PropType } from 'vue';
  
  export default defineComponent({
    props: {
      package: {
        type: Object as PropType<{ CodePkgs: string, NamePkg: string }>,
        required: true
      }
    },
    data() {
      return {
        pickedlist: false
      };
    }
  });
  </script>
  
 