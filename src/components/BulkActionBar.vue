<script lang="ts" setup>
import { useEmailSelection } from '../composition/useEmailSelection';
import { computed } from 'vue';

const props = defineProps<{
  emails: {
    archived: boolean;
    read: boolean;
    subject: string;
    from: string;
    body: string;
    sentAt: string;
  }[];
}>();

const emailSelection = useEmailSelection();

const numberSelected = computed(() => emailSelection.emails.size);
const allAreSelected = computed(() => props.emails.length == numberSelected.value && numberSelected.value !== 0);
const partialSelection = computed(() => numberSelected.value > 0 && !allAreSelected.value);

const bulkSelect = () => {
  if (allAreSelected.value) {
    emailSelection.clear();
  } else {
    emailSelection.addMultiple(props.emails)
  }
}
</script>
<template>
  <div class="bulk-action-bar">
    <span class="checkbox">
      <input type="checkbox" :checked="allAreSelected" :class="[partialSelection ? 'partial-check' : '']"
        @click="bulkSelect">
    </span>

    <span class="buttons">
      <button @click="emailSelection.markRead()" :disabled="Array.from(emailSelection.emails).every(e => e.read)">
        Mark Read
      </button>
      <button @click="emailSelection.markUnread()" :disabled="Array.from(emailSelection.emails).every(e => !e.read)">
        Mark Unread
      </button>
      <button v-if="selectedScreen == 'inbox'" @click="emailSelection.archive()" :disabled="numberSelected == 0">
        Archive
      </button>
      <button v-else @click="emailSelection.moveToInbox()" :disabled="numberSelected == 0">
        Move to Inbox
      </button>
    </span>
  </div>
</template>

<style scoped></style>