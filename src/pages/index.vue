<script setup lang="ts">
import Form from "../components/form.vue";
import { useDividasStore } from "@/stores/divida";
import DividaTable from "../components/divida-table.vue";
import { computed, readonly, ref } from "vue";
import FSelect from "../ui/f-select.vue";
import FInput from "@/ui/f-input.vue";
import Modal from "../ui/f-modal.vue";
import Dialog from "../ui/f-dialog.vue";
import FBtn from "@/ui/f-btn.vue";

const filter = ref("todas");
const value = ref();
const createFormVisibility = ref(false);

const items = readonly([
  { value: "pago", label: "Pago" },
  { value: "pendente", label: "Pendente" },
  { value: "todas", label: "Todas" },
]);

const store = useDividasStore();
const dividasPagas = computed(() => {
  if (filter.value === "todas") {
    return store.dividas;
  }
  const pago = filter.value === "pago";
  return store.dividas.filter((divida) => divida.pago === pago);
});
</script>
<template>
  <Modal v-model:visible="createFormVisibility">
    <Form @save="createFormVisibility = false" />
  </Modal>
  <DividaTable :rows="dividasPagas">
    <template #filter>
      <tr>
        <td colspan="1">
          <FSelect :items="items" v-model="filter" />
        </td>
        <!-- <td>
          <FInput placeholder="Digite..." />
        </td>
        <td>
          <FSelect :items="items" :value="value" />
        </td>

        <td>
          <FInput placeholder="Digite..." type="date" />
        </td> -->
        <td colspan="6" class="text-right">
          <FBtn @click="createFormVisibility = true"> Nova Dívida </FBtn>
        </td>
      </tr>
    </template>
  </DividaTable>
</template>
