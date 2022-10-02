<template>
  <div class="card">
    <div class="card-header">
      <strong>{{favoritedRestaurants.length}}</strong> 收藏的餐廳
    </div>
    <div class="card-body">
      <a href="/restaurants/81" v-for="favoritedRestaurant in favoritedRestaurants" :key="favoritedRestaurant.id">
        <img :src="favoritedRestaurant.image" width="60" height="60" class="avatar mr-2 p-1" />
      </a>
    </div>
  </div>
</template>
<script>
  export default{
    props: {
      favoritedRestaurants: {
        type: Array,
        required: true
      }
    }
  }
</script>