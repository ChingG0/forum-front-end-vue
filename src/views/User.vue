<template>
  <div class="container py-5">
    <UserProfileCard :isCurrentUser="currentUser.id === user.id" :user="user" :initIsFollowed="isFollowed" />
    <div class="row">
      <div class="col-md-4">
        <UserFollowingsCard :followings="followings" />
        <br />
        <UserFollowersCard :followers="followers" />
      </div>
      <div class="col-md-8">
        <UserCommentsCard :comments="comments" />
        <br />
        <UserFavoritedRestaurantsCard :favorited-restaurants="favorites" />
      </div>
    </div>

  </div>
</template>
<script>
import UserProfileCard from '../components/User/UserProfileCard.vue';
import UserFollowingsCard from '../components/User/UserFollowingsCard.vue';
import UserFollowersCard from '../components/User/UserFollowersCard.vue';
import UserCommentsCard from '../components/User/UserCommentsCard.vue';
import UserFavoritedRestaurantsCard from '../components/User/UserFavoritedRestaurantsCard.vue';
import usersAPI from './../apis/users'
import { Toast } from './../utils/helpers'
import { mapState } from 'vuex';

export default {
  name: 'User',
  components: {
    UserProfileCard,
    UserFollowingsCard,
    UserFollowersCard,
    UserCommentsCard,
    UserFavoritedRestaurantsCard
  },
  data() {
    return {
      user: {
        id: -1,
        name: '',
        email: '',
        image: '',
        followingsLength: 0,
        followersLength: 0,
        commentsLength: 0,
        favoritedRestaurantsLength: 0,
      },
      followings: [],
      followers: [],
      comments: [],
      favorites: [],
      isFollowed: false
    }
  },
  created() {
    const {id} = this.$route.params
    this.fetchUser(id)
  },
  computed: {
    ...mapState(['currentUser'])
  },
  beforeRouteUpdate (to, from, next) {
    const { id} = to.params
    this.fetchUser(id)
    next()
  },
  methods: {
    async fetchUser(userId) {
      try {
        const { data } = await usersAPI.get({ userId })
        console.log(data)
        const {profile, isFollowed} = data
        const { id, name, email, image, Comments, FavoritedRestaurants, Followers, Followings } = profile
        
        const commentSet = new Set()
        this.comments = Comments.filter(comment => comment.Restaurant && !commentSet.has(comment.Restaurant.id) && commentSet.add(comment.Restaurant.id))

        this.user = {
          ...this.user,
          id,
          name,
          email,
          image,
          followingsLength: Followings.length,
          followersLength: Followers.length,
          commentsLength: Comments.length,
          CommentsLength: this.comments.length,
          favoritedRestaurantsLength: FavoritedRestaurants.length,         
        }
        this.followings = Followings
        this.followers = Followers
        this.favorites = FavoritedRestaurants
        this.isFollowed = isFollowed
      }
      catch (err) {
        console.log(err)
        Toast.fire({
          icon: 'error',
          title: '無法取得使用者資料，請稍後再試'
        })
      }
    }
  }
}
</script>