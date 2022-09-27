<template>
  <div ref="root" @keydown.esc="closeDropDown">
    <div class="relative w-full">
      <input
        :value="props.modelValue"
        @input="debounceSearch"
        @focus="onHandlerDropDownOpen"
        ref="input"
        type="text"
        class="border-2 border-teal-700 py-1 pr-6 pl-5 w-full rounded-md"
        placeholder="Selected..."
      />

      <span
        @click="clearSelectedOption"
        tabindex="0"
        class="absolute right-2 top-2/4 translate-y-[-50%] cursor-pointer"
        >&times;
      </span>
    </div>

    <div
      v-if="dropDownIsOpen"
      class="border-2 w-full p-2 max-h-[200px] overflow-auto"
    >
      <ul class="flex flex-col gap-3 items-center">
        <li
          v-for="item in options"
          :key="item.id"
          @click="selectOption(item.country)"
          class="cursor-pointer max-w-fit hover:text-cyan-600"
        >
          {{ item.country }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup lang="ts">
import {
  defineProps,
  defineEmits,
  onMounted,
  ref,
  computed,
  WritableComputedRef,
} from 'vue';

type Options = {
  id: string | number;
  country: string | number;
};

interface Props {
  modelValue: string;
  debounceMs?: number;
  options: Options[];
}

const props = defineProps<Props>();
const emit = defineEmits(['update:modelValue']);

const root = ref<null | Node>(null);
const input = ref<null | HTMLInputElement>(null);
const timeout = ref<null | number>(null);
const dropDownIsOpen = ref(false);

const localModelValue: WritableComputedRef<string | number> = computed({
  get: () => props.modelValue,
  set: (val: string | number) => {
    emit('update:modelValue', val);
  },
});

const onHandlerDropDownOpen = (): void => {
  dropDownIsOpen.value = true;
};

const closeDropDown = () => {
  dropDownIsOpen.value = false;
  input.value?.blur();
};

const selectOption = (option: string | number): void => {
  localModelValue.value = option;
  dropDownIsOpen.value = false;
};

const clearSelectedOption = () => {
  localModelValue.value = '';
  dropDownIsOpen.value = true;
  input.value?.focus();
};

const debounceSearch = (event: Event): void => {
  if (timeout.value) clearTimeout(timeout.value);
  timeout.value = setTimeout(() => {
    emit('update:modelValue', (event.target as HTMLInputElement).value);
  }, props.debounceMs || 500);
};

onMounted(() => {
  window.addEventListener('click', ({ target }: MouseEvent) => {
    if (!root.value?.contains(target as Element)) {
      dropDownIsOpen.value = false;
    }
  });
});
</script>
