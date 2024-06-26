
<script setup lang="ts">
import { z } from 'zod';
import { reactive, ref, computed } from 'vue';
import axios from 'axios';

interface Category {
  id: number;
  title: string;
  slug: string;
  description: string;
  created_at: string;
  updated_at: string;
  deleted_at: string;
  parent_id: number;
  parent_category: Category;
}

const categories = ref<Category[]>([]);

const getCategories = async () => {
  try {
    const response = await $fetch<Category[]>(`http://127.0.0.1:8000/api/blog/categories/get`);
    categories.value = response;
    if (categories.value.length > 0) {
      state.parent_id = categories.value[0].id.toString();
    }
    console.log(categories.value);
  } catch (error) {
    console.error('Error fetching categories:', error);
  }
};
getCategories();

// Computed property for category options
const categoryOptions = computed(() => {
  return categories.value.map(category => ({
    value: category.id.toString(), // Convert id to string
    label: category.title
  }));
});

// Define Zod schema for validation
const schema = z.object({
  title: z.string().min(5, 'Title must be at least 5 characters').max(200, 'Title must be less than 200 characters'),
  slug: z.string().max(200, 'Slug must be less than 200 characters').optional(),
  description: z.string().min(3, 'Description must be at least 3 characters').max(500, 'Description must be less than 500 characters').optional(),
  parent_id: z.string().refine(value => {
    const numberValue = Number(value);
    return Number.isInteger(numberValue) && categories.value.some(category => category.id === numberValue);
  }, 'Parent ID must be a valid integer and exist in categories')
});

type Schema = z.infer<typeof schema>;

const state = reactive<Partial<Schema>>({
  title: undefined,
  slug: undefined,
  description: undefined,
  parent_id: undefined
});

async function onSubmit () {
  try {
    const result = schema.parse(state);
    console.log(result);

    // Convert parent_id to number before sending to server
    const dataToSend = {
      ...result,
      parent_id: Number(result.parent_id)
    };

    // Send the data to the server
    const response = await axios.post('http://127.0.0.1:8000/api/blog/categories', dataToSend);
    alert("Category successfully created!");
    window.location.href = '/categories'
  } catch (e) {
    if (e instanceof z.ZodError) {
      // Handle validation errors
      console.error(e.errors);
    } else {
      // Handle other errors
      console.error('Error submitting form:', e);
    }
  }
}
</script>
<template>
  <div class="w-1/2 m-auto">
    <div class="p-5">
      <nuxt-link class="mt-20 bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded" to="/categories">Back</nuxt-link>
    </div>
    <div class="p-5 border-2 rounded">
      <h1 class="text-center mb-2">Create category</h1>
      <UForm :schema="schema" :state="state" class="space-y-4" @submit="onSubmit">
        <UFormGroup label="Title" name="title">
          <UInput v-model="state.title" />
        </UFormGroup>

        <UFormGroup label="Slug" name="slug">
          <UInput v-model="state.slug" />
        </UFormGroup>

        <UFormGroup label="Description" name="description">
          <UInput v-model="state.description" />
        </UFormGroup>

        <UFormGroup label="Parent Category" name="parent_id">
          <USelect v-model="state.parent_id" :options="categoryOptions" />
        </UFormGroup>

        <UButton class="flex m-auto mt-5" type="submit">
          Create
        </UButton>
      </UForm>
    </div>
  </div>
</template>
<style>

</style>