<script setup>
import { ref, watch, computed } from 'vue';
import { useRouter, useRoute } from 'vue-router';
import { useQuery, useMutation, useQueryClient } from '@tanstack/vue-query';
import ContactForm from '@/components/ContactForm.vue';
import contactsService from '@/services/contacts.service';

const props = defineProps({
  contactId: { type: String, required: true },
});

const router = useRouter();
const route = useRoute();
const queryClient = useQueryClient();
const message = ref('');

const { data: contact, isError } = useQuery({
  queryKey: computed(() => ['contact', props.contactId]),
  queryFn: () => contactsService.fetchContact(props.contactId),
});

const stopWatch = watch(isError, (val) => {
  if (val) {
    router.push({
      name: 'notfound',
      params: { pathMatch: route.path.split('/').slice(1) },
      query: route.query,
      hash: route.hash,
    });
    stopWatch();
  }
});

const { mutate: updateContact } = useMutation({
  mutationFn: (formData) =>
    contactsService.updateContact(formData.get('id'), formData),
  onSuccess: () => {
    message.value = 'Liên hệ được cập nhật thành công.';
    queryClient.invalidateQueries({ queryKey: ['contacts'] });
  },
});

const { mutate: deleteContact } = useMutation({
  mutationFn: (id) => contactsService.deleteContact(id),
  onSuccess: () => {
    queryClient.invalidateQueries({ queryKey: ['contacts'] });
    router.push({ name: 'contactbook' });
  },
});

function onUpdateContact(formData) {
  updateContact(formData);
}

function onDeleteContact(id) {
  if (confirm('Bạn muốn xóa Liên hệ này?')) {
    deleteContact(id);
  }
}
</script>

<template>
  <div v-if="contact" class="page">
    <h4>Hiệu chỉnh Liên hệ</h4>
    <ContactForm
      :contact="contact"
      @submit:contact="onUpdateContact"
      @delete:contact="onDeleteContact"
    />
    <p>{{ message }}</p>
  </div>
</template>