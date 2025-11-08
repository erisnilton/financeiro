<script setup lang="ts">
import { ref } from "vue";
import FInput from "@/ui/f-input.vue";
import FBtn from "@/ui/f-btn.vue";
import DividaService from "@/services/divida/divida.service";
import type { Divida } from "@/services/divida/divida.model";
import type { CrudCreate } from "@/services/crud";
import { useDividasStore } from "@/stores/divida";
import FCheckbox from "../ui/f-checkbox.vue";
import FDialog from "../ui/f-dialog.vue";
import { create } from "domain";

const emit = defineEmits<{
  (event: "save", item: Divida): void;
}>();

const store = useDividasStore();

const form = ref<CrudCreate<Divida>>({
  pessoa: "",
  nome: "",
  valor: 0,
  pago: false,
  createdAt: "",
});

async function save() {
  const result = await store.save({
    ...form.value,
    createdAt: new Date(form.value.createdAt).setUTCHours(3, 0, 0, 0),
  });
  form.value = {
    pessoa: "",
    nome: "",
    valor: 0,
    pago: false,
    createdAt: "",
  };
  emit("save", result);
}
</script>

<template>
  <!-- <pre>
    {{ store.divida }}
</pre> -->
  <!-- <hr class="mb-8" /> -->
  <form @submit.prevent="save" style="width: 100vw; max-width: 480px">
    <FDialog class="fill-w">
      <template #header>
        <h3>Nova Dívida</h3>
      </template>
      <div>
        <FInput
          v-model="form.nome"
          label="Divida"
          placeholder="Digite..."
          class="mr-2"
        />
        <FInput
          v-model="form.pessoa"
          label="Pessoa"
          placeholder="Digite..."
          class="mr-2"
        />
        <FInput
          v-model="form.valor"
          label="Valor"
          placeholder="Digite..."
          type="number"
          step="0.01"
          class="mr-2"
        />
        <FInput
          v-model="form.createdAt"
          label="Data criação"
          placeholder="Digite..."
          type="date"
          class="mr-2"
        />
        <FCheckbox label="Pago" v-model="form.pago"></FCheckbox>
      </div>
      <template #footer>
        <FBtn type="submit">Salvar</FBtn>
      </template>
    </FDialog>
  </form>
</template>
