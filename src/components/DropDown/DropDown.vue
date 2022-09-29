<template>
  <div ref="root" @keydown.esc="closeDropDown" class="select-none">
    <SelectInput
      v-model="localModelValue"
      :dropDownIsOpen="dropDownIsOpen"
      :onKeyDown="onKeyDown"
      :clearSelectedOption="clearSelectedOption"
      @onHandlerDropDownOpen="dropDownIsOpen = true"
    />

    <OptioncsBlock
      :dropDownIsOpen="dropDownIsOpen"
      :options="options"
      :currentOptionIndexToFocus="currentOptionIndexToFocus"
      :localModelValue="localModelValue"
      :onKeyUp="onKeyUp"
      :onKeyDown="onKeyDown"
      :selectOption="selectOption"
      @setLengthOptionsData="onUpdateLengthOptionsData"
    />
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
  watch,
} from 'vue';
import SelectInput from './SelectInput/SelectInput.vue';
import OptioncsBlock from './OptioncsBlock/OptioncsBlock.vue';

type Options = {
  id: string | number;
  country: string;
};

interface Props {
  modelValue: string;
  options: Options[];
}

const props = defineProps<Props>();
const emit = defineEmits(['update:modelValue', 'tested']);

const root = ref<null | Node>(null);

const dropDownIsOpen = ref<boolean>(false);
const currentSelectedOption = ref<string>('');
const currentOptionIndexToFocus = ref<number>(0);
const lengthOptionsData = ref<number>(0);

const onUpdateLengthOptionsData = (value: number) =>
  (lengthOptionsData.value = value);

const closeDropDown = (): void => {
  dropDownIsOpen.value = false;
};

const selectOption = (option: string): void => {
  localModelValue.value = option;
  currentSelectedOption.value = option;
  dropDownIsOpen.value = false;
};

const clearSelectedOption = (): void => {
  localModelValue.value = '';
  currentSelectedOption.value = '';
  currentOptionIndexToFocus.value = 0;
  dropDownIsOpen.value = true;
};

const onKeyUp = (): void => {
  if (currentOptionIndexToFocus.value > 1) {
    currentOptionIndexToFocus.value -= 1;
  } else {
    currentOptionIndexToFocus.value = lengthOptionsData.value;
  }
};

const onKeyDown = (): void => {
  if (currentOptionIndexToFocus.value < lengthOptionsData.value) {
    currentOptionIndexToFocus.value += 1;
  } else {
    currentOptionIndexToFocus.value = 1;
  }
};

const localModelValue: WritableComputedRef<string> = computed({
  get: () => props.modelValue,
  set: (val: string) => {
    emit('update:modelValue', val);
  },
});

watch(dropDownIsOpen, (value) => {
  if (!value) {
    currentOptionIndexToFocus.value = 0;
  }
});

onMounted(() => {
  window.addEventListener('click', ({ target }: MouseEvent) => {
    if (!root.value?.contains(target as Element)) {
      dropDownIsOpen.value = false;
      localModelValue.value = currentSelectedOption.value;
    }
  });
});
</script>
