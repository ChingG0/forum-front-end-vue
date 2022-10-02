<template>
  <div>
    <Spinner v-if="isLoading" />
    <form v-else @submit.stop.prevent="handleSubmit" v-show="!isLoading">
      <div class="form-group">
        <label for="name">Name</label>
        <input id="name" v-model="restaurant.name" type="text" class="form-control" name="name" placeholder="Enter name"
          required>
      </div>

      <div class="form-group">
        <label for="categoryId">Category</label>
        <select id="categoryId" v-model="restaurant.categoryId" class="form-control" name="categoryId" required>
          <option value="" selected disabled>
            --請選擇--
          </option>
          <option v-for="category in categories" :key="category.id" :value="category.id">
            {{category.name}}
          </option>
        </select>
      </div>

      <div class="form-group">
        <label for="tel">Tel</label>
        <input id="tel" v-model="restaurant.tel" type="text" class="form-control" name="tel"
          placeholder="Enter telephone number">
      </div>

      <div class="form-group">
        <label for="address">Address</label>
        <input id="address" v-model="restaurant.address" type="text" class="form-control" placeholder="Enter address"
          name="address">
      </div>

      <div class="form-group">
        <label for="opening-hours">Opening Hours</label>
        <input id="opening-hours" v-model="restaurant.openingHours" type="time" class="form-control"
          name="opening_hours">
      </div>

      <div class="form-group">
        <label for="description">Description</label>
        <textarea id="description" v-model="restaurant.description" class="form-control" rows="3" name="description" />
      </div>

      <div class="form-group">
        <label for="image">Image</label>
        <img v-if="restaurant.image" :src="restaurant.image" class="d-block img-thumbnail mb-3" width="200"
          height="200" />
        <input id="image" type="file" name="image" accept="image/*" class="form-control-file"
          @change="handleFileChange">
      </div>

      <button type="submit" class="btn btn-primary mr-5" :disabled="isProcessing">
        {{isProcessing ? '處理中' : '送出'}}
      </button>
      <button type="button" class="btn btn-secondary" @click="$router.back()">回上一頁</button>
    </form>
  </div>
</template>

<script>
import { Toast } from '../../utils/helpers'
import adminAPI from '../../apis/admin'
import Spinner from '../Spinner.vue'

export default {
  name: 'AdminRestaurantFrom',
  props: {
    initRestaurant: {
      type: Object,
      default: () => {
        return {
          name: '',
          tel: '',
          address: '',
          openingHours: '',
          description: '',
          image: '',
          CategoryId: '',
        }
      }
    },
    isProcessing: {
      type: Boolean,
      default: false
    }
  },
  components: {
    Spinner
  },
  data() {
    return {
      categories: [],
      restaurant: {
        ...this.initRestaurant
      },
      isLoading: true
    }
  },
  watch: {
    /* eslint-disable */
    initRestaurant(newvalue, oldValue) {
      this.restaurant = {
        ...this.restaurant,
        ...newvalue
      }
    }
  },
  created() {
    this.fetchCategories()
  },
  methods: {
    async fetchCategories() {
      try {
        const { data } = await adminAPI.categories.get()

        this.categories = data.categories
        this.isLoading = false
      }
      catch (err) {
        this.isLoading = false

        Toast.fire({
          icon: 'error',
          title: '無法取得餐廳類別，請稍候再試'
        })
      }
    },
    handleFileChange(e) {
      const { files } = e.target
      if (files.length === 0) {
        this.restaurant.image = ''
        return
      } else {
        const imageURL = window.URL.createObjectURL(files[0])
        this.restaurant.image = imageURL
      }
    },
    handleSubmit(e) {
      if (!this.restaurant.name) {
        Toast.fire({
          icon: 'warning',
          title: '請填寫餐廳名稱'
        })
        return
      } else if (!this.restaurant.categoryId) {
        Toast.fire({
          icon: 'warning',
          title: '請選擇餐廳類別'
        })
        return
      }

      const form = e.target
      const formData = new FormData(form)
      this.$emit('after-submit', formData)
    }
  }
}
</script>