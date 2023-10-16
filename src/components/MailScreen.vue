<script lang="ts" setup>
import axios from 'axios';

import MailTable from '@/components/MailTable.vue';
import BulkActionBar from '@/components/BulkActionBar.vue';
import { useEmailSelection } from '../composition/useEmailSelection';
import { computed, onBeforeMount, ref } from 'vue';

const emails = ref([]);
const selectedScreen = ref('inbox');
const emailSelection = useEmailSelection();

onBeforeMount(async () => {
  const response = await axios.get('http://localhost:5173/emails');
  emails.value = response.data;
});

const sortedEmails = computed(() =>
  emails.value.sort((e1, e2) => {
    return e1.sentAt < e2.sentAt ? 1 : -1
  })
);
const unarchivedEmails = computed(() =>
  sortedEmails.value.filter(e => !e.archived)
);
const archivedEmails = computed(() => {
  sortedEmails.value.filter(e => e.archived)
});
const filteredEmails = computed(() => {
  let filters = {
    inbox: unarchivedEmails.value,
    archive: archivedEmails.value
  }
  return filters[selectedScreen.value]
});

const selectScreen = (newScreen) => {
  selectedScreen.value = newScreen;
  emailSelection.clear();
};
const capitalize = (word) => {
  if (!word || !word.length) { return; }

  return word[0].toUpperCase() + word.slice(1)
}
</script>

<template>
  <button @click="selectScreen('inbox');" :class="[selectedScreen == 'inbox' ? 'selected' : '']">
    Inbox View
  </button>
  <button @click="selectScreen('archive')" :class="[selectedScreen == 'archive' ? 'selected' : '']">
    Archived View
  </button>

  <h1>VMail {{ capitalize(selectedScreen) }}</h1>

  <BulkActionBar :emails="filteredEmails" :selectedScreen="selectedScreen" />

  <MailTable :emails="filteredEmails" />
</template>

<style scoped></style>