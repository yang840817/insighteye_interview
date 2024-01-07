<template>
  <q-page class="row justify-left q-pa-lg">
    <div class="block-item">
      <div class="row justify-between items-center">
        <div class="text-h6">員工基本資訊</div>
        <div>
          <q-btn
            class="q-mr-sm"
            color="primary"
            label="新增"
            @click="isAddDialog = true"
          />
          <q-btn
            color="white"
            text-color="black"
            label="刪除"
            :disable="!selected.length"
            @click="isDeleteDialog = true"
          />
        </div>
      </div>
      <QTable
        :columns="state.columns"
        :rows="state.rows"
        v-model:selected="selected"
        selection="multiple"
        :selected-rows-label="getSelectedString"
        rows-per-page-label="每頁筆數"
        :rows-per-page-options="[10, 20, 30]"
        :loading="loading"
        :filter="filter"
        binary-state-sort
        @onFilter="onFilter"
      >
      </QTable>
      <AddDialog
        v-model:isAddDialog="isAddDialog"
        @onCancel="onCancelAdd"
        @onConfirm="onConfirmAdd"
      />
      <DeleteDialog
        v-model:isDeleteDialog="isDeleteDialog"
        v-model:selected="selected"
        @onCancel="onCancelDelete"
        @onConfirm="onConfirmDelete"
      />
    </div>
  </q-page>
</template>
<script setup>
import { ref, reactive, onMounted } from "vue";
import QTable from "src/components/QTable.vue";
import DeleteDialog from "src/components/DeleteDialog.vue";
import AddDialog from "src/components/AddDialog.vue";
import { api } from "src/boot/axios.js";
import { date } from "quasar";

const selected = ref([]);
const isAddDialog = ref(false);
const isDeleteDialog = ref(false);
const filter = ref("");
const loading = ref(false);
const originData = ref([]);
const state = reactive({
  columns: [
    {
      name: "name",
      label: "姓名",
      align: "left",
      field: "name",
      sortable: true,
    },
    {
      name: "cellphone",
      label: "手機",
      align: "left",
      field: "cellphone",
      sortable: true,
    },
    {
      name: "email",
      label: "信箱",
      align: "left",
      field: "email",
      sortable: true,
    },
    {
      name: "gender",
      label: "性別",
      align: "left",
      field: "gender",
      sortable: true,
    },
    {
      name: "birthday",
      label: "生日",
      align: "left",
      field: "birthday",
      sortable: true,
    },
  ],
  rows: [],
});

onMounted(async () => {
  loading.value = true;
  const data = await fetchMembers();
  originData.value = data.map((item) => {
    return { ...item, birthday: formatBirthday(item.birthday) };
  });
  loading.value = false;
  state.rows = [...originData.value];
});

async function fetchMembers() {
  try {
    const { data } = await api.get(`/members`);
    return data.members;
  } catch (error) {
    return Promise.reject(error);
  }
}
async function fetchFilterMembers(
  requestPayload = {
    filter: {},
    sort: "",
  }
) {
  try {
    const { data } = await api.post(`/members/search`, requestPayload);
    return data.members;
  } catch (error) {
    return Promise.reject(error);
  }
}
function fetchFromServer(filter) {
  // 依column分別call api搜尋，生日格式特殊不搜尋
  const filterKeyList = ["name", "cellphone", "email", "gender"];
  if (filter) {
    return Promise.all(
      filterKeyList.map((key) => {
        const payload = {
          filter: { [key]: filter },
          sort: null,
        };
        return fetchFilterMembers(payload);
      })
    ).then((array) => {
      const flattenedArray = array.flatMap((innerArray) => innerArray);
      const filterResult = flattenedArray.reduce(
        (accumulator, currentValue) => {
          const existingObject = accumulator.find(
            (obj) => obj.name === currentValue.name
          );
          if (!existingObject) {
            accumulator.push(currentValue);
          }
          return accumulator;
        },
        []
      );
      return filterResult;
    });
  } else {
    return originData.value;
  }
}
function formatBirthday(value) {
  return date.formatDate(
    value.replace(/(\d{4})-(\d{1,2})-(\d{1,2})/, (match, year, month, day) => {
      return (
        year +
        "-" +
        (month.length === 1 ? "0" + month : month) +
        "-" +
        (day.length === 1 ? "0" + day : day)
      );
    }),
    "YYYY-MM-DD"
  );
}

function getSelectedString() {
  return "";
}
function onCancelAdd() {
  isAddDialog.value = false;
}
function onConfirmAdd(data) {
  isAddDialog.value = false;
  originData.value.unshift(data);
  state.rows = [...originData.value];
}
function onCancelDelete() {
  isDeleteDialog.value = false;
}
function onConfirmDelete(data) {
  originData.value = originData.value.filter((item1) => {
    return !data.some((item2) => item2.name === item1.name);
  });
  state.rows = [...originData.value];
  selected.value = [];
  isDeleteDialog.value = false;
}
function onFilter(input) {
  filter.value = input;
  loading.value = true;
  setTimeout(async () => {
    // api搜尋，暫用本地搜尋故註解
    // const returnedData = await fetchFromServer(filter.value);
    // state.rows = returnedData.map((item) => {
    //   return { ...item, birthday: formatBirthday(item.birthday) };
    // });

    loading.value = false;
  }, 1500);
}
</script>
<style lang="scss" scoped>
.block-item {
  min-width: 400px;
}
</style>
