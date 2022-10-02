<template>
  <div class="card mb-3">
    <div class="row no-gutters">
      <div class="col-md-4">
        <img :src="user.image | emptyImage" width="300px" height="300px" />
      </div>
      <div class="col-md-8">
        <div class="card-body">
          <h5 class="card-title">{{user.name}}</h5>
          <p class="card-text">
            {{user.email}}
          </p>
          <ul class="list-unstyled list-inline">
            <li><strong>{{user.commentsLength}}</strong> 已評論餐廳</li>
            <li><strong>{{user.FavoritedRestaurantsLength}}</strong> 收藏的餐廳</li>
            <li><strong>{{user.FollowingsLength}}</strong> followings (追蹤者)</li>
            <li><strong>{{user.FollowersLength}}</strong> followers (追隨者)</li>
          </ul>
          <p>
            <template v-if="isCurrentUser">
              <router-link :to="{name: 'editprofile', params: {id: user.id}}">
                <button class="btn btn-primary">
                  Edit
                </button>
              </router-link>
            </template>
            <template v-else>
              <button v-if="isFollowed" @click.prevent.stop="deleteFollowing(user.id)" type="button" class="btn btn-danger">
                unFollow
              </button>
              <button v-else @click.prevent.stop="addFollowing(user.id)" type="button" class="btn btn-primary">
                Follow
              </button>
            </template>
          </p>
        </div>
      </div>
    </div>
  </div>
</template>
<script>

import { emptyImageFilter } from '../../utils/mixins'
import usersAPI from '../../apis/users'
import { Toast } from '../../utils/helpers'

export default {
  props: {
    user: {
      type: Object,
      required: true
    },
    isCurrentUser:{
      type: Boolean,
      required: true
    },
    initIsFollowed: {
      type: Boolean,
      required: true
    }
  },
  mixins: [emptyImageFilter],

  data() {
    return {
      isFollowed: this.initIsFollowed
    }
  },
  watch: {
    initIsFollowed(isFollowed){
      this.isFollowed = isFollowed
    }
  },
  methods: {
    async addFollowing(userId) {
      try{
        const {data} = await usersAPI.addFollowing({userId})
        if(data.status !== 'sucess'){
          throw new Error(data.message)
        }
        this.isFollowed = true
      }
      catch(err){
        console.log(err)
        Toast.fire({
          icon: 'error',
          title: '無法追蹤，請稍後再試'
        })
      }
    },
    async deleteFollowing(userId) {
      try{
        const {data} = await usersAPI.deleteFollowing({userId})
        
        if(data.status !== 'sucess'){
          throw new Error(data.message)
        }
        this.isFollowed = false
      }
      catch(err){
        console.log(err)
        Toast.fire({
          icon: 'error',
          title: '無法取消追蹤，請稍後再試'
        })
      }
      
    }
  }
}
</script>
