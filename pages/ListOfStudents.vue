<script setup lang="ts">
import {ref, computed} from 'vue';
const columns = [
  {key: 'title', label: 'Title',sortable: true},
  {key: 'description', label: 'Description',sortable: true},
  {key: 'price', label: 'Price',sortable: true},
  {key: 'rating', label: 'Rating',sortable: true},
  {key: 'brand', label: 'Brand',sortable: true},
  {key: 'category', label: 'Category',sortable: true},
  {key: 'thumbnail', label: 'Thumbnail',sortable: true}
]
const products = [
  {
    title: 'iPhone 1',
    description: 'An apple mobile which is nothing like apple',
    price: 549,
    rating: 4.69,
    brand: 'Apple',
    category: 'smartphones',
    thumbnail: 'https://cdn.dummyjson.com/product-images/1/thumbnail.jpg'
  },
  {
    title: 'iPhone 2',
    description: 'An apple mobile which is nothing like apple',
    price: 549,
    rating: 4.69,
    brand: 'Apple',
    category: 'smartphones',
    thumbnail: 'https://cdn.dummyjson.com/product-images/1/thumbnail.jpg'
  },
  {
    title: 'iPhone 3',
    description: 'An apple mobile which is nothing like apple',
    price: 549,
    rating: 4.69,
    brand: 'Apple',
    category: 'smartphones',
    thumbnail: 'https://cdn.dummyjson.com/product-images/1/thumbnail.jpg'
  },
  {
    title: 'iPhone 4',
    description: 'An apple mobile which is nothing like apple',
    price: 549,
    rating: 4.69,
    brand: 'Apple',
    category: 'smartphones',
    thumbnail: 'https://cdn.dummyjson.com/product-images/1/thumbnail.jpg'
  },
  {
    title: 'iPhone 5',
    description: 'An apple mobile which is nothing like apple',
    price: 549,
    rating: 4.69,
    brand: 'Apple',
    category: 'smartphones',
    thumbnail: 'https://cdn.dummyjson.com/product-images/1/thumbnail.jpg'
  },
  {
    title: 'iPhone 6',
    description: 'An apple mobile which is nothing like apple',
    price: 549,
    rating: 4.69,
    brand: 'Apple',
    category: 'smartphones',
    thumbnail: 'https://cdn.dummyjson.com/product-images/1/thumbnail.jpg'
  },
  {
    title: 'iPhone 7',
    description: 'An apple mobile which is nothing like apple',
    price: 549,
    rating: 4.69,
    brand: 'Apple',
    category: 'smartphones',
    thumbnail: 'https://cdn.dummyjson.com/product-images/1/thumbnail.jpg'
  },
  {
    title: 'iPhone 8',
    description: 'An apple mobile which is nothing like apple',
    price: 549,
    rating: 4.69,
    brand: 'Apple',
    category: 'smartphones',
    thumbnail: 'https://cdn.dummyjson.com/product-images/1/thumbnail.jpg'
  },
  {
    title: 'iPhone 9',
    description: 'An apple mobile which is nothing like apple',
    price: 549,
    rating: 4.69,
    brand: 'Apple',
    category: 'smartphones',
    thumbnail: 'https://cdn.dummyjson.com/product-images/1/thumbnail.jpg'
  }
]
const q = ref('')
const page = ref(1)
const pageCount = 5
const total = ref(products.length)
const filteredRows = computed(() => {
  if (!q.value) {
    total.value = products.length
    return products.slice((page.value - 1) * pageCount, (page.value) * pageCount);
  }
  page.value = 1;
  let result = products.filter((product: any) => {
    return Object.values(product).some(value =>
        String(value).toLowerCase().includes(q.value.toLowerCase())
    );
  })
  total.value = result.length
  return result.slice((page.value - 1) * pageCount, (page.value) * pageCount);
});
</script>

<template>
  <div>
    <div class="flex px-3 py-3.5 border-b border-gray-200 dark:border-gray-700">
      <UInput v-model="q" placeholder="Filter products..."/>
    </div>
    <UTable class="w-full" :columns="columns" :rows="filteredRows">
      <template #thumbnail-data="{ row }">
        <img class="w-[100px] h-[100px]" :src="row.thumbnail" alt="Thumbnail" />
      </template>
    </UTable>
    <div class="flex justify-end px-3 py-3.5 border-t border-gray-200 dark:border-gray-700">
      <UPagination v-model="page" :page-count="pageCount" :total="total" />
    </div>
  </div>
</template>