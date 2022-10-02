<template>
  <div class="card">
    <div class="card-header">
      <strong>{{followings.length}}</strong> followings (追蹤者)
    </div>
    <div class="card-body">
      <a href="/users/51" v-for="following in followings" :key="following.id">
        <img :src="following.image" width="60" height="60" class="avatar mr-2" />
      </a>
    </div>
    
  </div>
</template>
<script>
  export default{
    props: {
      followings: {
        type: Array,
        required: true
      }
    },
  }
</script>