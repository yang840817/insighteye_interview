<template>
  <q-dialog
    :model-value="props.isDeleteDialog"
    @update:model-value="$emit('onCancel')"
  >
    <q-card>
      <q-card-section class="row items-center q-px-sm q-pt-sm q-pb-none">
        <q-space />
        <q-btn icon="close" flat round dense @click="$emit('onCancel')" />
      </q-card-section>

      <q-card-section class="items-center text-center q-px-xl q-py-none">
        <p class="text-h4 q-mb-lg">刪除</p>
        <p class="text-h5 q-mb-none">
          是否確定刪除 {{ props.selected?.length }} 筆資料?
        </p>
      </q-card-section>

      <div align="center" class="q-pa-xl">
        <q-btn
          class="q-px-xl q-py-xs"
          label="取消"
          color="white"
          text-color="black"
          outline
          @click="$emit('onCancel')"
        />
        <q-btn
          class="q-px-xl q-py-xs q-ml-md"
          label="確定"
          color="primary"
          @click="$emit('onConfirm', props.selected)"
        />
      </div>
    </q-card>
  </q-dialog>
</template>
<script setup>
const props = defineProps({
  isDeleteDialog: {
    type: Boolean,
  },
  selected: {
    type: Array,
    default: () => [],
  },
});
const emit = defineEmits(["onCancel", "onConfirm"]);
</script>
