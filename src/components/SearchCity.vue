<template>
  <div class="q-pa-md">
    <div class="q-gutter-md" style="max-width: 300px">
      <q-input
        @update:model-value="onChangeSearchInput"
        v-model="text"
        outlined
        label="Location..."
      />
    </div>
  </div>
</template>

<script setup>
import { debounce } from 'quasar';
import { ref } from 'vue';

const emit = defineEmits(['fetchItems']);

const text = ref('');

const onChangeSearchInput = debounce(() => {
  emit('fetchItems', text.value);
}, 300);
</script>
