<template>
  <div class="m-3">
    <div class="row">
      <div class="col-md-4 mb-2">
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
            :reduce="(option) => option.id"
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
        <div>
          produce name + time etc
        </div>
        <div class="row">
          <div class="col-md-6">
            ingredients
          </div>
          <div class="col-md-6">
            used in
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import products from './data/products.json'
import buildings from './data/buildings.json'
import vSelect from 'vue-select'
import 'vue-select/dist/vue-select.css';

export default {
  name: 'crk-produce',
  components: {
    vSelect
  },
  data(){
    return{
      products: products,
      buildings: buildings,
      options: products,

      selectedBuilding: '',
      selectedGoods: ''
    }
  },
  methods: {
    onChangeBuilding() {
      console.log('aaadasda')
      console.log(this.selectedBuilding)
    }
  }
}
</script>

<style scoped>

</style>
