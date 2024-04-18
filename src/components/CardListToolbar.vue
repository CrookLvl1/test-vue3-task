<template>
  <div class="toolbar">
    <v-tooltip
      v-for="layoutPiece in toolbarLayout"
      :text="layoutPiece.tooltip"
      :key="layoutPiece.tooltip"
    >
      <template v-slot:activator="{ props }">
        <v-btn
          :class="{ 'btn-pressed': layoutPiece.active.value && !sortButtonsDisabled }"
          :disabled="sortButtonsDisabled"
          v-bind="props"
          icon="mdi-sort"
          variant="tonal"
          class="sort-btn"
          color="blue"
          @click="sortList(layoutPiece.sortFnc, layoutPiece.active)"
        />
      </template>
    </v-tooltip>
  </div>
</template>

<script setup>
import { computed, ref } from "vue";

const props = defineProps({
  list: {
    type: Array,
    required: true,
  },
});

const list = computed(() => props.list);

const sortButtonsDisabled = computed(() => props.list.length < 2);

//  type: 'descending' (по убыванию) | 'ascending' (по возрастанию);
const getSortFunction = (type) => {
  switch (type) {
    case "descending": {
      return (a, b) => b.rating.rate - a.rating.rate;
    }
    case "ascending": {
      return (a, b) => a.rating.rate - b.rating.rate;
    }
    default: {
      return (a, b) => a.id - b.id;
    }
  }
};

const sortList = (sortFnc, btnState) => {
  list.value.sort(sortFnc);
  toolbarLayout.forEach((toolbarPiece) => (toolbarPiece.active.value = false));
  btnState.value = true;
};

const toolbarLayout = [
  {
    tooltip: "Сортировка по рейтингу по убыванию",
    sortFnc: getSortFunction("descending"),
    active: ref(false),
  },
  {
    tooltip: "Сортировка по рейтингу по возрастанию",
    sortFnc: getSortFunction("ascending"),
    active: ref(false),
  },
  {
    tooltip: "Сортировка по умолчанию (без сортировки)",
    sortFnc: getSortFunction(),
    active: ref(true),
  },
];
</script>

<style lang="scss" scoped>
.toolbar {
  min-height: 50px;
  display: flex;
  align-items: center;
  justify-content: right;
  width: 90%;
  gap: 10px;
  margin-bottom: 10px;
}

.btn-pressed {
  background-color: lightcoral;
}
</style>
