<template>
  <!-- Сюда можно перетащить ингредиент -->
  <app-drop @drop="$emit('drop', $event)">
    <div class="pizza" :class="`pizza--foundation--${foundation}`">
      <div class="pizza__wrapper">
        <div
          v-for="item in fillings"
          :key="item.id"
          class="pizza__filling"
          :class="[
            `pizza__filling--${item.name}`,
            { 'pizza__filling--second': item.count === 2 },
            { 'pizza__filling--third': item.count === 3 },
          ]"
        ></div>
      </div>
    </div>
  </app-drop>
</template>

<script setup>
import { computed } from "vue";
import AppDrop from "@/common/components/AppDrop.vue";
import ingredientsMap from "@/common/data/ingredients";

const props = defineProps({
  dough: {
    type: String,
    required: true,
  },
  sauce: {
    type: String,
    required: true,
  },
  // Объект вида { id ингредиента: количество }
  ingredients: {
    type: Object,
    required: true,
  },
});

defineEmits(["drop"]);

// Тонкое тесто — маленькая основа, толстое — большая
const foundation = computed(() => {
  const size = props.dough === "light" ? "small" : "big";
  return `${size}-${props.sauce}`;
});

// Только ингредиенты, которые добавлены на пиццу
const fillings = computed(() =>
  Object.entries(props.ingredients)
    .filter(([, count]) => count > 0)
    .map(([id, count]) => ({ id, name: ingredientsMap[id], count }))
);
</script>

<style lang="scss" scoped>
.pizza {
  position: relative;

  display: block;

  box-sizing: border-box;
  width: 100%;

  background-repeat: no-repeat;
  background-position: center;
  background-size: 100%;

  &--foundation--big-creamy { background-image: url("@/assets/img/foundation/big-creamy.svg"); }
  &--foundation--big-tomato { background-image: url("@/assets/img/foundation/big-tomato.svg"); }
  &--foundation--small-creamy { background-image: url("@/assets/img/foundation/small-creamy.svg"); }
  &--foundation--small-tomato { background-image: url("@/assets/img/foundation/small-tomato.svg"); }
}

.pizza__wrapper {
  width: 100%;
  padding-bottom: 100%;
}

.pizza__filling {
  position: absolute;
  top: 0;
  left: 0;

  display: block;

  width: 100%;
  height: 100%;

  background-repeat: no-repeat;
  background-position: center;
  background-size: 100%;

  &::before,
  &::after {
    display: none;

    position: absolute;
    top: 0;
    left: 0;

    width: 100%;
    height: 100%;

    content: "";

    background-image: inherit;
  }

  &--second::before {
    display: block;
    transform: rotate(45deg);
  }

  &--third::before {
    display: block;
    transform: rotate(45deg);
  }

  &--third::after {
    display: block;
    transform: rotate(-45deg);
  }

  &--ananas { background-image: url("@/assets/img/filling-big/ananas.svg"); }
  &--bacon { background-image: url("@/assets/img/filling-big/bacon.svg"); }
  &--blue_cheese { background-image: url("@/assets/img/filling-big/blue_cheese.svg"); }
  &--cheddar { background-image: url("@/assets/img/filling-big/cheddar.svg"); }
  &--chile { background-image: url("@/assets/img/filling-big/chile.svg"); }
  &--ham { background-image: url("@/assets/img/filling-big/ham.svg"); }
  &--jalapeno { background-image: url("@/assets/img/filling-big/jalapeno.svg"); }
  &--mozzarella { background-image: url("@/assets/img/filling-big/mozzarella.svg"); }
  &--mushrooms { background-image: url("@/assets/img/filling-big/mushrooms.svg"); }
  &--olives { background-image: url("@/assets/img/filling-big/olives.svg"); }
  &--onion { background-image: url("@/assets/img/filling-big/onion.svg"); }
  &--parmesan { background-image: url("@/assets/img/filling-big/parmesan.svg"); }
  &--salami { background-image: url("@/assets/img/filling-big/salami.svg"); }
  &--salmon { background-image: url("@/assets/img/filling-big/salmon.svg"); }
  &--tomatoes { background-image: url("@/assets/img/filling-big/tomatoes.svg"); }
}
</style>