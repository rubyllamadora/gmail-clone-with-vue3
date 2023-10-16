<script lang="ts" setup>
import { format } from 'date-fns';
import { marked } from 'marked';

const props = defineProps<{
  email: {
    id: string;
    archived: boolean;
    read: boolean;
    subject: string;
    from: string;
    body: string;
    sentAt: string;
  };
  changeEmail: Function;
}>();
</script>

<template>
  <div class="email-display">
    <h2 class="mb-0">Subject: <strong>{{ email.subject }}</strong></h2>
    <div><em>From {{ email.from }} on {{ format(new Date(email.sentAt), 'MMM do yyyy') }}</em></div>
    <div v-html="marked(email.body)" />
  </div>
</template>

<style scoped></style>