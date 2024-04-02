<script setup lang="ts">
useHead({
  title: 'Student List'
})
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
},{
  key: 'thumbnail',
  label: 'Thumbnail'
}]

const sort = ref({ column: 'title', direction: 'asc' as const })
const selectedColumns = ref([...columns])
const columnsTable = computed(() => columns.filter((column) => selectedColumns.value.includes(column)))
const { data, pending } = await useLazyAsyncData<any>('products', () => $fetch('https://dummyjson.com/products'))

let products = data.value.products;

// Selectable product, current page number, and count of elements on page
const selected = ref([])
const page = ref(1)
const pageCount = 4
// Filter and pagination
const q = ref('')
const sortedRows = computed(() => {
  const sortedProducts = [...products]
  const { column, direction } = sort.value

  if (column && direction) {
    sortedProducts.sort((a, b) => {
      const aValue = a[column]
      const bValue = b[column]
      if (aValue < bValue) return direction === 'asc' ? -1 : 1
      if (aValue > bValue) return direction === 'asc' ? 1 : -1
      return 0
    })
  }

  return sortedProducts
})

// Compute filtered rows based on the search query
const filteredRows = computed(() => {
  if (!q.value) {

    return sortedRows.value;
  }
  page.value = 1;
  return sortedRows.value.filter(product => {
    return Object.values(product).some(value => {
      return String(value).toLowerCase().includes(q.value.toLowerCase())
    })
  })
})

// Compute paginated rows based on the filtered data
const rows = computed(() => {
  const startIndex = (page.value - 1) * pageCount
  const endIndex = startIndex + pageCount

  return filteredRows.value.slice(startIndex, endIndex)
})

// Compute total count for pagination based on the filtered data length
const total = computed(() => filteredRows.value.length)
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
          v-model:sort="sort"
          sort-mode="manual"
          class="w-full "
          :ui="{ td: { base: 'max-w-[0] max-h-[0] truncate' } }"
  >
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
    <UPagination v-model="page" :page-count="pageCount" :total="total" />
  </div>
</template>

<style scoped>

</style>
