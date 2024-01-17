
<script>
import axios from 'axios';
import CreateDeal from '../src/components/CreateDeal.vue';

export default {
  components: {
    CreateDeal,
  
  },
  data() {
    return {
      accessData: '' 
    };
  },
  methods: {
    redirectToZoho() {
     const authURL = 'http://localhost:8000/zoho/redirect';
      window.open(authURL, "_blank");
    },

    handleAuthorizationCode() {

    const urlParams = new URLSearchParams(window.location.search);
    const authorizationCode = urlParams.get('code');

    if (authorizationCode) {
      axios.post('http://localhost:8000/exchange-code', { code: authorizationCode })
        .then(response => {

          this.accessData = response.data;
          console.log('Access token received:', this.accessData);

        })
        .catch(error => {
          console.error('Error exchanging code for access token:', error);
        });
    }
   },
  },

  mounted() {
    this.handleAuthorizationCode();
  }
}
</script>

<template>
  <div class="zoho-app">

    <button class="login-zoho" v-if="!this.accessData.access_token" @click="redirectToZoho">Login with Zoho</button>
    <div  v-else >
      <CreateDeal  v-if="accessData" :accessData="accessData"  />
    </div>
  </div>

</template>

<style scoped>
form{
  display: flex;
  flex-direction: column;
  gap: 10px;
}
.login-zoho{
padding: 30px;
border: none;
background: fuchsia;
color: white;
font-size: 20px;
border-radius: 5px;
cursor: pointer;
transition: .3s;
}
.login-zoho:hover{
background: rgb(245, 59, 59);
}
.zoho-app{
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}


</style>
