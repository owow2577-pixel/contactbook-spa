<script setup>
import { ref } from 'vue';
import ContactForm from '@/components/ContactForm.vue';
import contactsService from '@/services/contacts.service';

const message = ref('');

const emptyContact = {
  name: '',
  email: '',
  address: '',
  phone: '',
  favorite: false,
};

async function onAddContact(contact) {
  try {
    await contactsService.createContact(contact);
    message.value = 'Liên hệ đã được thêm thành công.';
  } catch (error) {
    console.log(error);
    message.value = 'Thêm liên hệ thất bại.'+ error.message;
  }
}
</script>

<template>
  <div class="page">
    <h4>Thêm Liên hệ</h4>
    <ContactForm
      :contact="emptyContact"
      @submit:contact="onAddContact"
    />
    <p>{{ message }}</p>
  </div>
</template>