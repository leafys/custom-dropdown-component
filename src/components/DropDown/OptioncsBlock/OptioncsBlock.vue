<template>
  <Transition>
    <div
      v-if="dropDownIsOpen"
      class="border-2 w-full p-2 max-h-[200px] overflow-auto"
    >
      <ul class="flex flex-col gap-3 items-center">
        <li
          v-for="(item, index) in filteredAndSorteredOptions"
          :key="item.id"
          @click="selectOption(item.country)"
          @keydown.enter.prevent="selectOption(item.country)"
          @keydown.down.prevent="onKeyDown"
          @keydown.up.prevent="onKeyUp"
          :tabindex="index + 1"
          :ref="(el) => (items[index + 1] = (el as HTMLElement))"
          class="cursor-pointer max-w-fit hover:text-cyan-600"
        >
          {{ item.country }}
        </li>

        <li v-if="!filteredAndSorteredOptions.length">
          Sorry, no matching options
        </li>
      </ul>
    </div>
  </Transition>
</template>

<script lang="ts" setup>
import { computed } from '@vue/reactivity';
import { ComputedRef, defineProps, defineEmits, ref, watch } from 'vue';

type Options = {
  id: string | number;
  country: string;
};

interface Props {
  dropDownIsOpen: boolean;
  currentOptionIndexToFocus: number;
  options: Options[];
  localModelValue: string;
  onKeyUp: () => void;
  onKeyDown: () => void;
  selectOption: (option: string) => void;
}

const props = defineProps<Props>();
const emit = defineEmits(['setLengthOptionsData']);

const items = ref<HTMLElement[]>([]);

const filteredAndSorteredOptions: ComputedRef<Options[]> = computed(() => {
  const filteredOptions = props.options.filter((item) =>
    item.country.toLowerCase().includes(props.localModelValue.toLowerCase())
  );

  const sortedFilteredOptions = filteredOptions.sort((a, b) =>
    a.country.localeCompare(b.country)
  );

  return sortedFilteredOptions;
});

watch(
  () => props.currentOptionIndexToFocus,
  (val) => {
    if (val !== 0) {
      items.value[val].focus();
    }
  }
);

watch(
  () => filteredAndSorteredOptions.value.length,
  (val) => {
    emit('setLengthOptionsData', val);
  }
);
</script>

<style scoped lang="scss">
.v-enter-active {
  transition: opacity 0.5s ease;
}
.v-leave-active {
  transition: opacity 0.25s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}
</style>
