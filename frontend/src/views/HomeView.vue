<template>
  <main class="content">
    <form action="#" method="post">
      <div class="content__wrapper">
        <h1 class="title title--big">Конструктор пиццы</h1>

        <div class="content__dough">
          <div class="sheet">
            <h2 class="title title--small sheet__title">Выберите тесто</h2>

            <dough-selector v-model="pizza.dough" :items="doughItems" />
          </div>
        </div>

        <div class="content__diameter">
          <div class="sheet">
            <h2 class="title title--small sheet__title">Выберите размер</h2>

            <diameter-selector v-model="pizza.size" :items="sizeItems" />
          </div>
        </div>

        <div class="content__ingredients">
          <div class="sheet">
            <h2 class="title title--small sheet__title">
              Выберите ингредиенты
            </h2>

            <div class="sheet__content ingredients">
              <sauce-selector v-model="pizza.sauce" :items="sauceItems" />

              <ingredients-selector
                :items="ingredientItems"
                :values="pizza.ingredients"
                @update="updateIngredientAmount"
              />
            </div>
          </div>
        </div>

        <div class="content__pizza">
          <label class="input">
            <span class="visually-hidden">Название пиццы</span>
            <input
              v-model="pizza.name"
              type="text"
              name="pizza_name"
              placeholder="Введите название пиццы"
            />
          </label>

          <div class="content__constructor">
            <pizza-constructor
              :dough="pizza.dough"
              :sauce="pizza.sauce"
              :ingredients="pizza.ingredients"
              @drop="addIngredient"
            />
          </div>

          <div class="content__result">
             <p>Итого: {{ price }} ₽</p>
            <button type="button" class="button" :disabled="!canSubmit">
              Готовьте!
            </button>
          </div>
        </div>
      </div>
    </form>
  </main>
</template>

<script setup>
import { reactive, computed } from "vue";

import doughItems from "@/mocks/dough.json";
import ingredientItems from "@/mocks/ingredients.json";
import sauceItems from "@/mocks/sauces.json";
import sizeItems from "@/mocks/sizes.json";

import doughSizes from "@/common/data/doughSizes";
import saucesMap from "@/common/data/sauces";
import sizesMap from "@/common/data/sizes";

import { MAX_INGREDIENT_COUNT } from "@/common/constants";

import DoughSelector from "@/modules/constructor/DoughSelector.vue";
import DiameterSelector from "@/modules/constructor/DiameterSelector.vue";
import SauceSelector from "@/modules/constructor/SauceSelector.vue";
import IngredientsSelector from "@/modules/constructor/IngredientsSelector.vue";
import PizzaConstructor from "@/modules/constructor/PizzaConstructor.vue";

// Все выбранные параметры пиццы хранятся здесь
const pizza = reactive({
  name: "",
  dough: "light",
  size: "normal",
  sauce: "tomato",
  // { id ингредиента: количество }, сначала у всех 0
  ingredients: Object.fromEntries(ingredientItems.map(({ id }) => [id, 0])),
});

// Цена пиццы: (тесто + соус + ингредиенты) × множитель размера
const price = computed(() => {
  const dough = doughItems.find(({ id }) => doughSizes[id] === pizza.dough);
  const sauce = sauceItems.find(({ id }) => saucesMap[id] === pizza.sauce);
  const size = sizeItems.find(({ id }) => sizesMap[id] === pizza.size);

  const ingredientsPrice = ingredientItems.reduce(
    (sum, { id, price }) => sum + price * pizza.ingredients[id],
    0
  );

  return (dough.price + sauce.price + ingredientsPrice) * size.multiplier;
});

// Кнопка «Готовьте!» активна, если есть название и хотя бы один ингредиент
const canSubmit = computed(
  () =>
    pizza.name.trim() !== "" &&
    Object.values(pizza.ingredients).some((count) => count > 0)
);

const updateIngredientAmount = (id, count) => {
  pizza.ingredients[id] = count;
};

// Ингредиент перетащили на пиццу: добавляем одну порцию
const addIngredient = (ingredient) => {
  const count = pizza.ingredients[ingredient.id];
  if (count < MAX_INGREDIENT_COUNT) {
    pizza.ingredients[ingredient.id] = count + 1;
  }
};
</script>

<style lang="scss" scoped>
@import "@/assets/scss/ds-system/ds.scss";
@import "@/assets/scss/mixins/mixins.scss";

.content {
  padding-top: 20px;
}

.content__wrapper {
  display: flex;
  align-items: flex-start;
  flex-wrap: wrap;

  width: 920px;
  margin: 0 auto;
  padding-right: 2.12%;
  padding-bottom: 30px;
  padding-left: 2.12%;
}

.content__dough {
  width: 527px;
  margin-top: 15px;
  margin-right: auto;
  margin-bottom: 15px;
}

.content__diameter {
  width: 373px;
  margin-top: 15px;
  margin-bottom: 15px;
}

.content__ingredients {
  width: 527px;
  margin-top: 15px;
  margin-right: auto;
  margin-bottom: 15px;
}

.content__pizza {
  width: 373px;
  margin-top: 15px;
  margin-bottom: 15px;
}

.content__constructor {
  width: 315px;
  margin-top: 25px;
  margin-right: auto;
  margin-left: auto;
}

.content__result {
  display: flex;
  align-items: center;
  justify-content: center;

  margin-top: 25px;

  p {
    @include b-s24-h28;

    margin: 0;
  }

  button {
    margin-left: 12px;
    padding: 16px 45px;
  }
}

.sheet {
  padding-top: 15px;

  border-radius: 8px;
  background-color: $white;
  box-shadow: $shadow-light;
}

.sheet__title {
  padding-right: 18px;
  padding-left: 18px;
}

.sheet__content {
  display: flex;
  align-items: center;
  flex-wrap: wrap;

  margin-top: 8px;
  padding-top: 18px;
  padding-right: 18px;
  padding-left: 18px;

  border-top: 1px solid rgba($green-500, 0.1);
}

.title {
  box-sizing: border-box;
  width: 100%;
  margin: 0;

  color: $black;

  &--big {
    @include b-s36-h42;
  }

  &--small {
    @include b-s18-h21;
  }
}

.input {
  display: block;

  input {
    @include r-s16-h19;

    display: block;

    box-sizing: border-box;
    width: 100%;
    margin: 0;
    padding: 8px 16px;

    transition: 0.3s;

    color: $black;
    border: 1px solid $purple-400;
    border-radius: 8px;
    outline: none;
    background-color: $white;

    font-family: inherit;

    &:focus {
      border-color: $green-500;
    }
  }

  &:hover input {
    border-color: $black;
  }
}

.button {
  @include b-s18-h21;

  display: block;

  box-sizing: border-box;
  margin: 0;
  padding: 0;

  cursor: pointer;
  transition: 0.3s;
  text-align: center;

  color: $white;
  border: none;
  border-radius: 8px;
  outline: none;
  box-shadow: $shadow-medium;
  background-color: $green-500;

  font-family: inherit;

  &:hover:not(:active):not(:disabled) {
    background-color: $green-400;
  }

  &:active:not(:disabled) {
    background-color: $green-600;
  }

  &:disabled {
    cursor: default;

    color: rgba($white, 0.2);
    background-color: $green-300;
  }
}
</style>