<template>
  <div class="container py-5">
    <RestaurantDetail :initRestaurant="restaurant" />
    <hr>
    <RestaurantComments :restaurantComments="restaurantComments" @after-delete-comment="afterDeleteComment" />
    <CreateComment :restaurant-id="restaurant.id" @after-create-comment="afterCreateComment" />
  </div>
</template>

<script>
import RestaurantDetail from '../components/Restaurant/RestaurantDetail'
import RestaurantComments from '../components/Restaurant/RestaurantComments'
import CreateComment from '../components//Restaurant/CreateComment.vue'
import restaurantAPI from '../apis/restaurants'
import { Toast } from '../utils/helpers'
import { mapState } from 'vuex'


export default {
  name: 'Restaurant',
  components: {
    RestaurantDetail,
    RestaurantComments,
    CreateComment,
  },
  data() {
    return {
      restaurant: {
        id: -1,
        name: '',
        categoryName: '',
        image: '',
        openingHours: '',
        tel: '',
        address: '',
        description: '',
        isFavorited: false,
        isLiked: false,
      },
      restaurantComments: [],
    }
  },
  computed: {
    ...mapState(['currentUser'])
  },

  beforeRouteUpdate(to,from,next){
    const {id} = to.params
    this.fetchRestaurant(id)
    next()
  },

  created() {
    const { id } = this.$route.params
    this.fetchRestaurant(id)
  },

  methods: {
    async fetchRestaurant(restaurantId) {
      try {
        const { data } = await restaurantAPI.getRestaurant({ restaurantId })

        const { restaurant, isFavorited, isLiked } = data

        const { id, name, Category, image, opening_hours: openingHours, tel, address, description, Comments } = restaurant
        this.restaurant = {
          id,
          name,
          categoryName: Category ? Category.name : '未分類',
          image,
          openingHours: openingHours,
          tel,
          address,
          description,
          isFavorited,
          isLiked,
        }
        this.restaurantComments = Comments
      }
      catch (err) {
        console.log(err.message)
        Toast.fire({
          icon: 'error',
          title: '無法取得餐廳資料，請稍後再試'
        })
      }

    },
    afterDeleteComment(commentId) {
      this.restaurantComments = this.restaurantComments.filter(comment => comment.id !== commentId)
    },
    afterCreateComment(payload) {
      const { commentId, restaurantId, text } = payload
      this.restaurantComments.push({
        id: commentId,
        RestaurantId: restaurantId,
        User: {
          id: this.currentUser.id,
          name: this.currentUser.name,
        },
        text,
        createdAt: new Date()
      })
    }
  }
}
</script>