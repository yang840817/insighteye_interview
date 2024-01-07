<template>
  <q-dialog
    :model-value="props.isAddDialog"
    @update:model-value="$emit('onCancel')"
  >
    <q-card>
      <q-form @reset="onReset" @submit="onSubmit">
        <q-card-section class="row items-center q-px-md q-pt-md q-pb-none">
          <div class="text-subtitle1">新增員工資料</div>
          <q-space />
          <q-btn icon="close" flat round dense @click="$emit('onCancel')" />
        </q-card-section>
        <q-card-section class="items-center text-center q-px-md q-py-none">
          <q-input
            v-model="name"
            label="姓名 *"
            lazy-rules
            :rules="[
              (val) => (val && val.length > 0) || '此欄位必填！',
              (val) =>
                /^[A-Za-z\u4e00-\u9fa5 ]+$/.test(val) ||
                '請輸入中英文字，數字及特殊符號無效',
              (val) =>
                (val !== `null` && val !== `undefined`) ||
                '請勿輸入【null】or【undefined】',
            ]"
          />
          <q-input
            v-model="cellphone"
            label="手機 *"
            lazy-rules
            :rules="[
              (val) => (val && val.length > 0) || '此欄位必填！',
              (val) =>
                (val.replaceAll(`-`, ``).length >= 10 &&
                  !isNaN(Number(val.replaceAll(`-`, ``)))) ||
                '請輸入數字10碼以上，可此用【-】符號)',
            ]"
          />
          <q-input
            v-model="email"
            type="email"
            label="信箱 *"
            lazy-rules
            :rules="[
              (val) => (val && val.length > 0) || '此欄位必填！',
              (val) =>
                /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(val) || '請輸入正確信箱格式',
            ]"
          />
          <q-input
            v-model="gender"
            label="性別 *"
            lazy-rules
            :rules="[
              (val) => (val && val.length > 0) || '此欄位必填！',
              (val) =>
                val === `男` ||
                val === `女` ||
                val === `多元` ||
                '請輸入【男】or【女】or【多元】',
              ,
            ]"
          />
          <q-input
            v-model="birthday"
            type="date"
            label="生日 *"
            lazy-rules
            :rules="[(val) => (val && val.length > 0) || '此欄位必填！']"
          />
        </q-card-section>
        <q-card-section align="center" class="q-pb-md q-pt-md q-ml-none">
          <q-btn
            class="q-px-xl q-py-xs"
            label="重置"
            color="red"
            text-color="black"
            outline
            type="reset"
          />
          <q-btn
            class="q-px-xl q-py-xs q-ml-md"
            label="送出"
            color="primary"
            type="submit"
          />
        </q-card-section>
      </q-form>
    </q-card>
  </q-dialog>
</template>
<script setup>
import { ref } from "vue";

const props = defineProps({
  isAddDialog: {
    type: Boolean,
  },
});
const emit = defineEmits(["onCancel", "onConfirm"]);

const name = ref(null);
const cellphone = ref("");
const email = ref(null);
const gender = ref(null);
const birthday = ref(null);

function onReset() {
  name.value = null;
  cellphone.value = null;
  email.value = null;
  gender.value = null;
  birthday.value = null;
}
function onSubmit() {
  const formData = {
    name: name.value,
    cellphone: cellphone.value,
    email: email.value,
    gender: gender.value,
    birthday: birthday.value,
  };
  onReset();
  emit("onConfirm", formData);
}
</script>
