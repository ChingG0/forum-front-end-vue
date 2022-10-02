<template>
  <div>
    <h2 class="my-4">
      所有評論：
    </h2>
    <div v-for="comment in restaurantComments" :key="comment.id">
      <blockquote class="blockquote mb-0">
        <button v-if="currentUser.isAdmin" type="button" class="btn btn-danger float-right"
          @click.stop.prevent="handleDeleteButtonClick(comment.id)">
          Delete
        </button>
        <h3>
          <a href="#">
            {{ comment.User.name }}
          </a>
        </h3>
        <p>{{comment.text}}</p>
        <footer class="blockquote-footer">
          {{comment.createdAt | fromNow}}
        </footer>
      </blockquote>
      <hr>
    </div>
  </div>
</template>
<script>
import { fromNowFilter } from '../../utils/mixins'
import { mapState } from 'vuex'
import commentsAPI from '../../apis/comments'
import { Toast } from '../../utils/helpers'

export default {
  name: 'RestaurantComments',
  mixins: [fromNowFilter],
  props: {
    restaurantComments: {
      type: Array,
      required: true
    }
  },
  computed: {
    ...mapState(['currentUser'])
  },
  methods: {
    async handleDeleteButtonClick(commentId) {
      try {
        const { data } = await commentsAPI.delete({ commentId })

        if (data.status !== 'success') {
          throw new Error(data.message)
        }
        this.$emit('after-delete-comment', commentId)
        Toast.fire({
          icon: 'success',
          title: '成功刪除評論'
        })
      }
      catch (err) {
        console.log(err)
        Toast.fire({
          icon: 'error',
          title: '無法刪除評論，請稍後再試'
        })
      }
    }
  },
}
</script>

<style scoped>
  h2.my-4 {
    margin-bottom: 1rem !important;
    font-size: 18px;
  }
  
  h3 {
    margin-bottom: 3px;
    line-height: 1.3;
  }
  
  .blockquote-footer {
    font-size: 12.5px;
  }
  </style>