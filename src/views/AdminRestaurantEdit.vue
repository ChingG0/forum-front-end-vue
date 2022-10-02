<template>
  <div class="container py-5">
    <AdminRestaurantFrom :initRestaurant="restaurant" @after-submit="handleAfterSubmit" :is-processing="isProcessing"/>
  </div>
</template>
<script>
import AdminRestaurantFrom from '../components/Admin/AdminRestaurantFrom.vue';
import adminAPI from '../apis/admin';
import { Toast } from '../utils/helpers';

export default {
  components: {
    AdminRestaurantFrom
  },
  data() {
    return {
      restaurant: {
        id: 2,
        name: '',
        tel: '',
        address: '',
        openingHours: '',
        description: '',
        image: '',
        categoryId: '',
      },
      isProcessing: false
    }
  },
  created() {
    const { id } = this.$route.params
    this.fetchRestaurant(id)
  },
  beforeRouteUpdate(to,from,next){
    const {id} = to.params
    this.fetchRestaurant(id)
    next()
  },
  methods: {
    /*eslint-disable */
    async fetchRestaurant(restaurantId) {
      try {
        const { data } = await adminAPI.restaurants.getDetail({ restaurantId })
        const { id, name, tel, address, opening_hours: openingHours, description, image, CategoryId:categoryId } = data.restaurant
        this.restaurant = {
          ...this.restaurant,
          id,
          name,
          tel,
          address,
          openingHours,
          description,
          image,
          categoryId
        }
      }
      catch (err) {
        console.log(err)
        Toast.fire({
          icon: 'error',
          title: '無法取得餐廳，請稍後再試'
        })
      }
    },
    async handleAfterSubmit(formData) {
      try {
        this.isProcessing = true
        const { data } = await adminAPI.restaurants.update({ restaurantId: this.restaurant.id, formData })

        if (data.status !== 'success') {
          throw new Error(data.message)
        }
        this.$router.push({ name: 'admin-restaurants' })
      }
      catch (error) {
        this.isProcessing = false
        console.log(error)
        Toast.fire({
          icon: 'error',
          title: '無法更新餐廳資料，請稍候再試'
        })
      }
    }
  }
}
</script>