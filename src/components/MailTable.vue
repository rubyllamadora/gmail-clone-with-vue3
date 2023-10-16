<script lang="ts" setup>
import { format } from 'date-fns';
import MailView from '@/components/MailView.vue';
import axios from 'axios';
import { onBeforeMount, ref } from 'vue';

const openedEmail = ref(null);
const emails = ref([]);

const openEmail = (email) => {
  email.read = true;
  updateEmail(email);
  openedEmail.value = email;
};
const archiveEmail = (email) => {
  email.archived = true;
  updateEmail(email);
};

const sortedEmails = () => emails.value.sort((e1, e2) => e1.sentAt < e2.sentAt ? 1 : -1);

const unarchivedEmails = () => sortedEmails().filter(e => !e.archived);

const updateEmail = (email) => axios.put(`http://localhost:3002/emails/${email.id}`, email);

onBeforeMount(async () => {
  emails.value = (await axios.get('http://localhost:3002/emails')).data;
});

</script>

<template>
  <table class="mail-table">
    <tbody>
      <tr v-for="email in unarchivedEmails" :key="email.id" :class="['clickable', email.read ? 'read' : '']"
        @click="openEmail(email)">
        <td>
          <input type="checkbox" />
        </td>
        <td>{{ email.from }}</td>
        <td>
          <p><strong>{{ email.subject }}</strong> - {{ email.body }}</p>
        </td>
        <td class="date">{{ format(new Date(email.sentAt), 'MMM do yyyy') }}</td>
        <td><button @click="archiveEmail(email)">Archive</button></td>
      </tr>
    </tbody>
  </table>
  <MailView v-if="openedEmail" :email="openedEmail" />
</template>
