<template>
  <div class="p-4">
    <div class="row">
      <div class="col-md-4 mb-4">
        <div class="mb-3">
          <label>Filter by Building</label>
          <v-select 
            class="mt-1" 
            v-model="selectedBuilding" 
            label="name" 
            placeholder="Select building..." 
            :options="buildings"
            :reduce="(option) => option.id" />
        </div>
        <div>
          <label>Search Goods</label>
          <v-select 
            class="mt-1" 
            v-model="selectedGoods" 
            label="name" 
            placeholder="Select goods..." 
            :options="options"
          >
            <template #option="option">
              <div class="d-flex">
                <div style="width: 40px;">
                  <img :src="option.image_url" class="py-1" height="35px" />
                </div>
                <div class="my-auto">{{ option.name }}</div>
              </div>
            </template>
          </v-select>
        </div>
      </div>
      <div class="col-md-8">
        <div v-if="isSelected" class="d-flex align-items-center mb-4">
          <div class="p-2">
            <img :src="selectedGoods.image_url" width="50px">
          </div>
          <div class="p-2 flex-fill goods-title outline-text">{{ selectedGoods.name }}</div>
          <div class="p-2" style="text-align: end;">
            <div v-if="isSelectedGoods">
              <span><img class="title-icon" src="./assets/icons/clock.png"></span>
              <span>{{ formatTime(selectedGoods.production_time) }}</span>
            </div>
            <div>
              <span><img class="title-icon" src="./assets/icons/building.png"></span>
              <span>{{ getBuilding(selectedGoods.building_id) }}</span>
            </div>
          </div>
        </div>
        <div class="mt-3 row">
          <div v-if="isSelectedGoods" class="col-md-6 mb-4">
            <h5>Ingredients</h5>
            <div v-for="(ingredient, i) in selectedIngredients" :key="i">
              <div class="outline-text material-container d-flex align-items-center" @click="setSelected(ingredient.detail)">
                <div class="p-2">
                  <img :src="ingredient.detail.image_url" width="50px">
                </div>
                <div class="p-2 flex-fill">{{ ingredient.detail.name }} (x{{ ingredient.amount }})</div>
                <div v-if="ingredient.detail.is_goods">
                  <!-- break down btn -->
                </div>
              </div>
            </div>
          </div>
          <div v-if="isSelected" class="col-md-6 mb-3">
            <h5>Used In</h5>
            <div v-for="(prod, i) in selectedUsedIn" :key="i">
              <div class="outline-text material-container d-flex align-items-center" @click="setSelected(prod.detail)">
                <div class="p-2">
                  <img :src="prod.detail.image_url" width="50px">
                </div>
                <div class="p-2 flex-fill">{{ prod.detail.name }}</div>
                <div class="p-2">{{ prod.amount }}</div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import products from './data/products.json'
import product_ingredients from './data/product_has_ingredients.json'
import buildings from './data/buildings.json'
import vSelect from 'vue-select'
import 'vue-select/dist/vue-select.css';

export default {
  name: 'crk-produce',
  components: {
    vSelect
  },
  data(){
    return {
      products: products,
      buildings: buildings,
      options: products,

      selectedBuilding: '',
      selectedGoods: null,

      selectedIngredients: [],
      selectedUsedIn: [],
      isSelected: 0,
      isSelectedGoods: 0
    }
  },
  watch: {
    selectedBuilding(value) {
      this.selectedGoods = null
      if (value !== null) {
        this.options = this.products.filter((p) => p.building_id === value )
      } else {
        this.options = this.products
      }
    },
    selectedGoods(value) {
      if (value !== null) {
        this.isSelected = 1
        this.isSelectedGoods = value.is_goods
        this.getIngredients(value.id)
        this.getUsedIn(value.id)
      } else {
        this.isSelected = 0
        this.isSelectedGoods = 0
        this.selectedIngredients = []
        this.selectedUsedIn = []
      }
    }
  },
  methods: {
    formatTime(timeSec) {
      let hours = Math.floor(timeSec / 3600)
      let minutes = Math.floor((timeSec - (hours * 3600)) / 60)
      let seconds = timeSec - (hours * 3600) - (minutes * 60)

      let text = ''
      if (hours !== 0) text += `${hours}h `
      if (minutes !== 0) text += `${minutes}m `
      if (seconds !== 0) text += `${seconds}s `
      return text
    },
    getBuilding(id) {
      return this.buildings.find((b) => b.id === id).name
    },
    getIngredients(id) {
      let ingredients = product_ingredients.filter((i) => i.product_id === id)
      this.selectedIngredients = ingredients.map((i) => ({
        ...i,
        detail: products.find((p) => p.id === i.ingredient_id)
      }))
    },
    getUsedIn(id) {
      let used = product_ingredients.filter((i) => i.ingredient_id === id)
      this.selectedUsedIn = used.map((i) => ({
        ...i,
        detail: products.find((p) => p.id === i.product_id)
      }))
      console.log(this.selectedUsedIn)
    },
    setSelected(value) {
      this.selectedGoods = value
    }
  }
}
</script>

<style scoped>
.goods-title {
  font-size: x-large;
  font-weight: bold;
}
.title-icon {
  width: 20px;
  margin-right: 5px;
}

</style>
