<template>
  <div style="width: 250px;">
    <div>{{ selected }}</div>

    <input-select v-model="selected" :items="selectOptions" multiple filterable />

  </div>
</template>

<script>
  import InputSelect from './components/InputSelect.vue';

  export default {
    components: {
      InputSelect
    },
    data() {
      return {
        selected: '',
        selectOptions: [
          { id: '1', option: 'Option - 1' },
          { id: '2', option: 'Option - 2' },
          { id: '3', option: 'Option - 3' },
          { id: '4', option: 'Option - 4' },
          { id: '5', option: 'Option - 5' },
          { id: '6', option: 'Option - 6' },
          { id: '7', option: 'Option - 7' },
          { id: '8', option: 'Option - 8' },
          { id: '9', option: 'Option - 9' },
          { id: '10', option: 'Option - 10' },
          { id: '11', option: 'Option - 11' },
        ]
      }
    }
  }
</script>