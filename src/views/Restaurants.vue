<template>
    <div class="container py-5">
        <NavTabs />
        <RestaurantsNavPills :categories="categories" />
        <Spinner v-if="isLoading" />
        <template v-else>
            <div class="row">
                <RestaurantCard v-for="restaurant in restaurants" :key="restaurant.id" :init-restaurant="restaurant" />
            </div>
            
            <div v-if="restaurants.length < 1 ">
                此類別目前無餐廳
            </div>

            <RestaurantsPagination :current-page="currentPage" :total-page="totalPage" :previous-page="previousPage"
                :next-page="nextPage" :category-id='categoryId' />

        </template>
    </div>
</template>

<script>
import NavTabs from '../components/Restaurants/NavTabs.vue'
import RestaurantCard from '../components/Restaurants/RestaurantCard.vue'
import RestaurantsNavPills from '../components/Restaurants/RestaurantsNavPills.vue'
import RestaurantsPagination from '../components/Restaurants/RestaurantsPagination.vue'
import restaurantsAPI from '../apis/restaurants'
import { Toast } from '../utils/helpers'
import Spinner from '../components/Spinner.vue'

export default {
    name: 'Restaurants',
    components: {
        NavTabs,
        RestaurantCard,
        RestaurantsNavPills,
        RestaurantsPagination,
        Spinner
    },
    data() {
        return {
            restaurants: [],
            categories: [],
            categoryId: -1,
            currentPage: 1,
            totalPage: [],
            previousPage: -1,
            nextPage: -1,
            isLoading: true
        }
    },
    created() {
        const { page = '', categoryId = '' } = this.$route.query
        this.fetchRestaurants({ queryPage: page, queryCategoryId: categoryId })
    },
    beforeRouteUpdate(to, from, next) {
        const { page = '', categoryId = '' } = to.query
        this.fetchRestaurants({ queryPage: page, queryCategoryId: categoryId })
        next()
    },
    methods: {
        async fetchRestaurants({ queryPage, queryCategoryId }) {
            try {
                const response = await restaurantsAPI.getRestaurants({
                    page: queryPage,
                    categoryId: queryCategoryId
                })
                const { restaurants, categories, categoryId, page, totalPage, prev, next } = response.data
                this.restaurants = restaurants
                this.categories = categories
                this.categoryId = categoryId
                this.currentPage = page
                this.totalPage = totalPage
                this.previousPage = prev
                this.nextPage = next
                this.isLoading=false
            }
            catch (err) {
                this.isLoading=false
                console.log(err)
                Toast.fire({
                    icon: 'error',
                    title: '無法取得餐廳資料，請稍後再試'
                })
            }
        }
    }

}
</script>