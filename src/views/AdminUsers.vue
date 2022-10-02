<template>
  <div class="container py-5">
    <!-- AdminNav Component -->
    <AdminNav />

    <table class="table">
      <thead class="thead-dark">
        <tr>
          <th scope="col">
            #
          </th>
          <th scope="col">
            Email
          </th>
          <th scope="col">
            Role
          </th>
          <th scope="col" width="140">
            Action
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="user in users" :key="user.id">
          <th scope="row">
            {{user.id}}
          </th>
          <td> {{user.name}}</td>
          <td>{{user.role}}</td>
          <td>{{user.isAdmin? 'admin': 'user'}}</td>
          <td>
            <button v-if="currentUser.id !== user.id" type="button" class="btn btn-link"
              @click="toggleRole({userId: user.id, isAdmin: user.isAdmin})">
              {{user.isAdmin ? 'set as user' : 'set as admin'}}
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import AdminNav from '../components/Admin/AdminNav.vue';
import { mapState } from 'vuex';
import adminAPI from '../apis/admin';
import { Toast } from '../utils/helpers';

export default {
  name: 'AdminUsers',
  components: { AdminNav },
  data() {
    return {
      users: []
    }
  },
  created() {
    this.fetchData()
  },
  computed: {
    ...mapState(['currentUser'])
  },

  methods: {
    async fetchData() {
      try {
        const { data } = await adminAPI.users.get()
        
        this.users = data.users
      }
      catch (err) {
        console.log(err)
        Toast.fire({
          icon: 'error',
          title: '無法取得使用者資料，請稍後再試'
        })
      }
    },
    async toggleRole({ userId, isAdmin }) {
      try {
        const { data } = await adminAPI.users.update({ userId, isAdmin: (!isAdmin).toString() })

        if (data.status === 'error') {
          throw new Error(data.message)
        }

        this.users = this.users.map(user => {
          if (user.id === userId) {
            return {
              ...user,
              isAdmin: !user.isAdmin,
            }
          }
          return user
        })
      }
      catch (err) {
        console.log(err)
        Toast.fire({
          icon: 'error',
          title: '無法變更權限，請稍候再試'
        })
      }
    },
  }
}
</script>