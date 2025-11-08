<script setup lang="ts">
import type { Divida } from "@/services/divida/divida.model";
import type { Column } from "@/ui/f-table.vue";
import { computed, ref, watch } from "vue";
import FTable from "../ui/f-table.vue";
import FCheckbox from "../ui/f-checkbox.vue";
import { useDividasStore } from "@/stores/divida";
import FBtn from "../ui/f-btn.vue";
import FModal from "../ui/f-modal.vue";
import FDialog from "../ui/f-dialog.vue";

const props = defineProps<{ rows: Divida[] }>();
const store = useDividasStore();

const deleteDialog = ref({
  visible: false,
  item: null as Divida | null,
});

const total = computed(() =>
  props.rows
    .reduce((soma, item) => soma + item.valor, 0)
    .toLocaleString("pt-br", { style: "currency", currency: "BRL" })
);
const columns = ref<Column[]>([
  { id: "pago", title: "Pago", align: "center", get: () => null },
  { id: "nome", title: "Titulo", align: "left", get: (item) => item.nome },
  { id: "pessoa", title: "Pessoa", align: "left", get: (item) => item.pessoa },
  {
    id: "dataCriacao",
    title: "Data criação",
    align: "center",
    get: (item) =>
      item.createdAt && new Date(item.createdAt).toLocaleDateString("pt-BR"),
  },
  {
    id: "valor",
    title: "Valor",
    align: "right",
    get: (item) =>
      item.valor.toLocaleString("pt-br", {
        style: "currency",
        currency: "BRL",
      }),
  },
  { id: "actions", title: "Ações", align: "right", get: () => null },
]);
const rowsRef = computed(() => {
  return props.rows.map((item) => {
    const itemRef = ref(item);
    watch(
      itemRef,
      (newValue) => {
        store.save(newValue);
      },
      { deep: true }
    );
    return itemRef.value;
  });
});
async function confirmDelete() {
  await store.deleteById(deleteDialog.value.item?.id);
  deleteDialog.value = { visible: false, item: null };
}
</script>

<template>
  <FTable :columns="columns" :rows="rowsRef">
    <template #before-header>
      <slot name="filter"></slot>
    </template>
    <template #column-pago="{ row, index, column }">
      <td class="text-center">
        <FCheckbox v-model="row.pago"> </FCheckbox>
      </td>
    </template>
    <template #column-actions="{ row }">
      <td class="text-right">
        <FBtn @click="deleteDialog = { visible: true, item: row }"
          >Excluir</FBtn
        >
      </td>
    </template>
    <template #footer>
      <tr>
        <td colspan="4"></td>
        <th class="text-right">Total:</th>
        <td class="text-right">{{ total }}</td>
      </tr>
    </template>
  </FTable>

  <FModal v-model:visible="deleteDialog.visible">
    <FDialog>
      <template #header>
        <h3>Excluir dívida</h3>
      </template>
      <p>
        você tem certeza que deseja excluir a dívida "{{
          deleteDialog.item?.nome
        }}" no valor de
        {{
          deleteDialog.item?.valor?.toLocaleString("pt-BR", {
            currency: "BRL",
          })
        }}?
      </p>
      <template #footer>
        <FBtn
          class="mr-2"
          @click="deleteDialog = { visible: false, item: null }"
          >Não</FBtn
        >
        <FBtn @click="confirmDelete">Sim</FBtn>
      </template>
    </FDialog>
  </FModal>
</template>
