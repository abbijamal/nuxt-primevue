<script setup lang='ts'>

import { FilterMatchMode } from 'primevue/api'
const { tableData, filters, dataTableRef, exportCSV } = usePrimeDataTable()

filters.value = {
  global: { value: null, matchMode: FilterMatchMode.CONTAINS },
  name: { value: null, matchMode: FilterMatchMode.CONTAINS },
  code: { value: null, matchMode: FilterMatchMode.CONTAINS },
  inventoryStatus: { value: null, matchMode: FilterMatchMode.STARTS_WITH }
}

const { pending, data: products, error, refresh } = useLazyFetch('/api/products')

watch(products, (newProducts) => {
  console.log(newProducts.value)
  tableData.value = newProducts?.data
})

onMounted(() => {
  console.log('mounted')
  refresh()
})

</script>

<template>
  <div class="card">
    <h2>PrimeVue Demo DataTable</h2>
    <div v-if="error">
      <h5 class="text-red-500">
        Error
      </h5>
      <span class="text-red-500">{{ error }}</span>
    </div>
    <div v-if="pending">
      <h6>Loading ...</h6>
    </div>
    <div v-if="tableData">
      <DataTable
        ref="dataTableRef"
        v-model:filters="filters"
        :value="tableData"
        data-key="name"
        :global-filter-fields="['name', 'code', 'inventoryStatus']"
        striped-rows
        :paginator="true"
        :rows="8"
        paginator-template="CurrentPageReport FirstPageLink PrevPageLink PageLinks NextPageLink LastPageLink RowsPerPageDropdown"
        :rows-per-page-options="[8, 15, 50]"
        current-page-report-template="Showing {first} to {last} of {totalRecords}"
      >
        <template #header>
          <div class="datatable-header">
            <div class="flex justify-between">
              <span class="text-xl">Products</span>
              <span class="p-input-icon-left">
                <i class="pi pi-search" />
                <InputText v-model="filters.global.value" placeholder="Globale Suche" />
              </span>
            </div>
          </div>
        </template>
        <template #empty>
          No Data Found.
        </template>
        <Column field="name" header="Name" :sortable="true" />
        <Column field="code" header="Code" :sortable="true" />
        <Column field="price" header="Price" :sortable="true" />
        <Column field="inventoryStatus" header="Status" :sortable="true" />
        <template #footer>
          <div class="flex justify-between">
            <span class="text-2xl">{{ tableData ? tableData.length : 0 }} Products</span>
            <Button icon="pi pi-external-link" label="Export" @click="exportCSV" />
          </div>
        </template>
        <template #paginatorRight />
      </DataTable>
    </div>
  </div>
</template>

<style scoped></style>
