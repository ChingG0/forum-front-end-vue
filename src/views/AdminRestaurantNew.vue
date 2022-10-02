<template>
  <div class="container py-5">
     <!-- 餐廳表單 AdminRestaurantForm -->
     <AdminRestaurantFrom @after-submit="handleAfterSubmit" :is-processing="isProcessing"/>
   </div>
 </template>
 <script>
import AdminRestaurantFrom from '../components/Admin/AdminRestaurantFrom.vue';
import adminAPI from '../apis/admin';
import { Toast } from '../utils/helpers';

  export default{
    name: "AdminRestaurantNew",
    components: { AdminRestaurantFrom },
    data(){
      return {
        isProcessing: false
      }
    },
    methods:{
      async handleAfterSubmit(formData){
        try{
          this.isProcessing = true
          const {data} = await adminAPI.restaurants.create({formData})

          if(data.status !== 'success'){
            throw new Error(data.message)
          }
          this.$router.push({name: 'admin-restaurants'})
        } 
        catch(error){
          this.isProcessing = false
          Toast.fire({
            icon: 'error',
            title: '無法建立餐廳，請稍後再試'
          })
        }
      }
    }
}
 </script>