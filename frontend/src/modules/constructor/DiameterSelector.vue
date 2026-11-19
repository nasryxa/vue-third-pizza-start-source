<template>
  <div class="sheet__content diameter">
    <label
      v-for="size in items"
      :key="size.id"
      class="diameter__input"
      :class="`diameter__input--${sizes[size.id]}`"
    >
      <input
        type="radio"
        name="diameter"
        :value="sizes[size.id]"
        class="visually-hidden"
        :checked="modelValue === sizes[size.id]"
        @change="$emit('update:modelValue', sizes[size.id])"
      />
      <span>{{ size.name }}</span>
    </label>
  </div>
</template>

<script setup>
import sizes from "@/common/data/sizes";

defineProps({
  items: {
    type: Array,
    required: true,
  },
  modelValue: {
    type: String,
    required: true,
  },
});

defineEmits(["update:modelValue"]);
</script>

<style lang="scss" scoped>
@import "@/assets/scss/ds-system/ds.scss";
@import "@/assets/scss/mixins/mixins.scss";

.diameter__input {
  margin-right: 8.7%;
  margin-bottom: 20px;
  padding-top: 7px;
  padding-bottom: 6px;

  cursor: pointer;

  span {
    @include r-s16-h19;

    position: relative;

    padding-left: 46px;

    &::before {
      @include p_center-v;

      width: 36px;
      height: 36px;

      content: "";
      transition: 0.3s;

      border-radius: 50%;
      background-color: $green-100;
      background-image: url("@/assets/img/diameter.svg");
      background-repeat: no-repeat;
      background-position: center;
    }
  }

  &:nth-child(3n) {
    margin-right: 0;
  }

  &--small span::before {
    background-size: 18px;
  }

  &--normal span::before {
    background-size: 29px;
  }

  &--big span::before {
    background-size: 100%;
  }

  &:hover span::before {
    box-shadow: $shadow-regular;
  }

  input:checked + span::before {
    box-shadow: $shadow-large;
  }
}
</style>