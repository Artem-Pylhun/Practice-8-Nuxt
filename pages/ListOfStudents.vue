<script setup lang="ts">
import {useLazyAsyncData} from "#app";

const columns = [{
  key: 'title',
  label: 'Title',
  sortable: true
},{
  key: 'description',
  label: 'Description',
  sortable: true
},{
  key: 'price',
  label: 'Price',
  sortable: true
},{
  key: 'rating',
  label: 'Rating',
  sortable: true
},{
  key: 'brand',
  label: 'Brand',
  sortable: true
},{
  key: 'category',
  label: 'Category',
  sortable: true
},
  {
    key: 'thumbnail',
    label: 'Thumbnail',
    sortable: true
  }]

const selectedColumns = ref([...columns])
const columnsTable = computed(() => columns.filter((column) => selectedColumns.value.includes(column)))
const {data, pending} = await useLazyAsyncData<any>(
    'products', () => $fetch('https://dummyjson.com/products'))

let products = data.value.products;
//selectable product, current page number and count of elements on page
const selected = ref([products[1]])
const page = ref(2)
const pageCount = 4

// filter and pagination
const q = ref('')
const rows = computed(() => {
  if (!q.value) {
    return products.slice((page.value - 1) * pageCount, (page.value) * pageCount)
  }

  return products.filter((product: any) => {
    return Object.values(product).some((value) => {
      return String(value).toLowerCase().includes(q.value.toLowerCase())
    })
  })
})


</script>

<template>
  <div class="flex justify-between px-3 py-3.5 border-b border-gray-200 dark:border-gray-700">
    <UInput v-model="q" placeholder="Filter products..." />
    <USelectMenu v-model="selectedColumns" :options="columns" multiple>
      <UButton
          icon="i-heroicons-view-columns"
          color="gray"
          size="xs"
      >
        Columns
      </UButton>
    </USelectMenu>

  </div>
  <UTable :columns="columnsTable"
          :rows="rows"
          :loading="pending"
          v-model="selected"
          class="w-full "
          :ui="{ td: { base: 'max-w-[0] max-h-[0] truncate' } }">
    <!--displaying color that depends on rating value-->>
    <template class="" #rating-data="{ row }">
      <span :style="{ color: row.rating > 4.5 ? 'green' : 'red' }">{{ row.rating }}</span>
    </template>
    <!--displaying images 100x100-->>
    <template #thumbnail-data="{ row }">
      <img class="w-[100px] h-[100px]" :src="row.thumbnail" alt="Thumbnail" />
    </template>
  </UTable>
  <div class="flex justify-end px-3 py-3.5 border-t border-gray-200 dark:border-gray-700">
    <UPagination v-model="page" :page-count="pageCount" :total="products.length" />
  </div>
</template>

<style scoped>

</style>