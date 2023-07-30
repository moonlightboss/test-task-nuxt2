<template>
  <div>
    <v-text-field
      v-model="searchQuery"
      label="Search by name"
      solo-inverted
      hide-details
      dense
    />
  </div>
</template>

<script>
export default {
  props: {
    search: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      searchQuery: ''
    };
  },
  watch: {
    searchQuery(newVal) {
      this.$emit('update:search', newVal);
    }
  }
};
</script>
