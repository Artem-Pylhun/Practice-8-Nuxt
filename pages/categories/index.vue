<script setup lang="ts">
import {API_BASE_URL} from "~/utils/constants";
import axios from "axios";

const columns = [
  {
    key: 'id',
    label: '#'
  },
  {
    key: 'title',
    label: 'Назва',
  },
  {
    key: 'parent_category_title',
    label: 'Батьківська категорія'
  },
  {
    key: 'actions',
    label: 'Дії'
  }
]


const page = ref(1);
const page_count = 5;


interface Category {
  id: number;
  title: string;
  parent_category: Category;
  parent_id: number;
}

const categories = ref<Category[]>([]);

const getCategories = async () => {
  try {
    const response = await $fetch<Category[]>(API_BASE_URL + '/api/blog/categories');
    categories.value = response;
  } catch (error) {
    console.error('Error fetching posts:', error);
  }
};
getCategories();

const rows = computed(() => {
  return categories.value.map((category) => ({
    id: category.id,
    title: category.title,
    parent_category_title: category.parent_category?.id ?? 'N/A'
  }));
});

const total = computed(() => rows.value.length);
const paginated_rows = computed(() => {
      return rows.value.slice((page.value - 1) * page_count, (page.value) * page_count);
    }
);

const items = (category:Category) => [
  [{
    label: 'Редагувати',
    icon: 'i-heroicons-pencil-square-20-solid',
    click: () => window.location.href = (`/categories/update/${category?.id}`)
  }, {
    label: 'Деталі',
    icon: 'i-heroicons-information-circle-20-solid',
    click: () => window.location.href = (`/categories/${category?.id}`)
  }], [{
    label: 'Видалити',
    icon: 'i-heroicons-trash-20-solid',
    click: () => {
      if (confirm(`Ви впевнені, що хочете видалити категорію '${category.title}'?`))
      {
        axios.delete(`http://127.0.0.1:8000/api/blog/categories/${category.id}`)
            .then(res => {
              console.log(res);
              alert('Категорію видалено!');
              categories.value = categories.value.filter(c => c.id !== category.id);
            })
            .catch(error => {
              console.error(error);
              alert('Не вдалось видалити категорію!');
            });
      }
    }
  }]
]
</script>

<template>
  <div class="flex flex-col">
    <div class="me-5">
      <NuxtLink to="/categories/create">
        <button class="bg-transparent hover:bg-blue-500 float-end text-blue-700 font-semibold hover:text-white py-2 px-4 border border-blue-500 hover:border-transparent rounded">
          Add Category
        </button>
      </NuxtLink>
    </div>
    <div>

      <UTable class="mt-3" :columns="columns" :rows="paginated_rows" :total="rows.length">
        <template #actions-data="{ row }">
          <UDropdown :items="items(row)">
            <UButton color="gray" variant="ghost" icon="i-heroicons-ellipsis-horizontal-20-solid" />
          </UDropdown>
        </template>
      </UTable>
      <div class="d-flex mt-5">
        <UPagination v-model="page" :page-count="page_count" :total="total" />
      </div>
    </div>
  </div>
</template>