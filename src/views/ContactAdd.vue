<script setup>
import { ref } from 'vue';
import { useMutation, useQueryClient } from '@tanstack/vue-query';
import ContactForm from '@/components/ContactForm.vue';
import contactsService from '@/services/contacts.service';

const queryClient = useQueryClient();
const message = ref('');

const emptyContact = {
  name: '',
  email: '',
  address: '',
  phone: '',
  favorite: false,
};

const { mutate: createContact } = useMutation({
  mutationFn: (formData) => contactsService.createContact(formData),
  onSuccess: () => {
    message.value = 'Liên hệ đã được thêm thành công.';
    queryClient.invalidateQueries({ queryKey: ['contacts'] });
  },
  onError: (error) => {
    message.value = 'Thêm liên hệ thất bại.' + error.message;
  },
});

function onAddContact(formData) {
  createContact(formData);
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