<template>
  <div class="relative w-full">
    <input
      :value="props.modelValue"
      @input="debounceSearch"
      @focus="onHandlerDropDownOpen"
      @keydown.down.prevent="onKeyDown"
      ref="input"
      type="text"
      class="border-2 border-teal-700 py-1 pr-6 pl-5 w-full rounded-md"
      placeholder="Selected..."
    />

    <span
      v-if="modelValue.length || dropDownIsOpen"
      @click="localClearSelectedOption"
      @keydown.down.prevent="onKeyDown"
      @keydown.enter.prevent="localClearSelectedOption"
      tabindex="0"
      class="absolute right-2 top-2/4 translate-y-[-50%] cursor-pointer"
      >&times;
    </span>
  </div>
</template>

<script lang="ts" setup>
import { defineProps, defineEmits, ref, watch } from 'vue';

interface Props {
  modelValue: string;
  dropDownIsOpen: boolean;
  onKeyDown: () => void;
  clearSelectedOption: () => void;
}

const props = defineProps<Props>();
const emit = defineEmits(['update:modelValue', 'onHandlerDropDownOpen']);

const timeout = ref<null | number>(null);
const input = ref<null | HTMLInputElement>(null);

const onHandlerDropDownOpen = (): void => {
  emit('onHandlerDropDownOpen');
};

const localClearSelectedOption = (): void => {
  props.clearSelectedOption();
  input.value?.focus();
};

const debounceSearch = (event: Event): void => {
  if (timeout.value) clearTimeout(timeout.value);
  timeout.value = setTimeout(() => {
    emit('update:modelValue', (event.target as HTMLInputElement).value);
  }, 500);
};

watch(
  () => props.dropDownIsOpen,
  () => {
    if (props.dropDownIsOpen === false) {
      input.value?.blur();
    }
  }
);
</script>
