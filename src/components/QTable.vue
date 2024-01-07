<template>
  <q-table
    class="q-mt-sm"
    row-key="name"
    :columns="columns"
    :rows="rows"
    :filter="filter"
    separator="cell"
    flat
    bordered
  >
    <template v-slot:top-right>
      <q-input
        borderless
        dense
        debounce="1000"
        placeholder="Search"
        :model-value="filter"
        @update:model-value="$emit('onFilter', $event)"
      >
        <template v-slot:append>
          <q-icon name="search" />
        </template>
      </q-input>
    </template>
    <template v-slot:body-cell="props">
      <q-td :props="props">
        {{ props.value }}
        <q-tooltip
          class="text-caption"
          anchor="bottom middle"
          self="bottom middle"
        >
          {{ props.value }}
        </q-tooltip>
      </q-td>
    </template>
    <template v-slot:pagination="scope">
      <q-btn
        icon="chevron_left"
        color="grey-8"
        round
        dense
        flat
        :disable="scope.isFirstPage"
        @click="scope.prevPage"
      ></q-btn>
      {{ scope.pagination.page }}
      <q-btn
        icon="chevron_right"
        color="grey-8"
        round
        dense
        flat
        :disable="scope.isLastPage"
        @click="scope.nextPage"
      ></q-btn>
      {{ `共${rows.length}筆資料` }}
    </template>
  </q-table>
</template>
<script setup>
const props = defineProps({
    columns: {
      type: Array,
      default: () => [],
    },
    rows: {
      type: Array,
      default: () => [],
    },
    filter: {
      type: String,
      default: () => "",
    },
});
const emit = defineEmits(["onFilter"]);
</script>
<style lang="scss" scoped></style>
