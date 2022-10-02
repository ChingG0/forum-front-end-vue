<template>
  <div class="container py-5">
    <form class="w-100" @submit.prevent.stop="handleSubmit">
      <div class="text-center mb-4">
        <h1 class="h3 mb-3 font-weight-normal">
          Sign In
        </h1>
      </div>

      <div class="form-label-group mb-2">
        <label for="email">Email</label>
        <input id="email" name="email" v-model="email" type="email" class="form-control" placeholder="email"
          autocomplete="email" required>
      </div>

      <div class="form-label-group mb-3">
        <label for="password">Password</label>
        <input id="password" name="password" v-model="password" type="password" class="form-control"
          placeholder="Password" autocomplete="new-password" required>
      </div>

      <button class="btn btn-lg btn-primary btn-block mb-3" type="submit" :disabled="isProcessing">
        Submit
      </button>

      <div class="text-center mb-3">
        <p>
          <router-link to="/signup">
            Sign Up
          </router-link>
        </p>
      </div>

      <p class="mt-5 mb-3 text-muted text-center">
        &copy; 2022-2023
      </p>
    </form>
  </div>
</template>


<script>
import authorizationAPI from '../apis/authorization';
import { Toast } from './../utils/helpers'


export default {
  data() {
    return {
      email: '',
      password: '',
      isProcessing: false
    }
  },
  methods: {
    async handleSubmit() {
      try {
        if (!this.email || !this.password) {
          Toast.fire({
            icon: 'warning',
            title: 'Please enter Email and password'
          })
          return
        }
        this.isProcessing = true

        // TODO: 向後端驗證使用者登入資訊是否合法
        const {data} = await authorizationAPI.signIn({
          email: this.email,
          password: this.password
        })

        if (data.status !== 'success') {
          throw new Error(data.message)
        }

        localStorage.setItem('token', data.token)
        
        Toast.fire({
          icon: 'success',
          title: '登入成功！'
        })

        this.$store.commit('setCurrentUser', data.user)

        this.$router.push('/restaurants')
      }
      catch (error) {
        this.password = ''
        this.isProcessing = false
        Toast.fire({
          icon: 'warning',
          title: '帳號或密碼輸入錯誤！'
        })
        console.log(error)
      }
    }
  }
}
</script>