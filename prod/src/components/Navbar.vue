<template>
    <nav class="navbar fixed-top navbar-expand-lg navbar-light bg-light">
  <div class="container-fluid">
    
    <router-link :to="{ name: 'Index' }">
                    <img width="150" src="@/assets/logo_Far_350_c.png"></router-link>

                    <ul class="navbar-nav me-auto mb-2 mb-lg-0">
    <li class="nav-item">
          <router-link :to="{ name: 'Index' }" v-if="isLoggedIn" class="nav-link">Tableau de bord</router-link>
        </li>
         <li class="nav-item">
          <router-link :to="{ name: 'AddClient' }" v-if="isLoggedIn" class="nav-link">Ajouter client</router-link>
        </li>

        <li class="nav-item">
          <router-link :to="{ name: 'Articles' }" v-if="isLoggedIn" class="nav-link">Ajouter articles</router-link>
        </li>

       

       
      </ul>
    <form class="d-flex">
      
     
      <a v-if="isLoggedIn"><button v-on:click="logout" class="btn btn-outline-primary">Déconnecter</button></a>
                
    </form>
  </div>
</nav>
     
    
</template>

<script>
import firebase from 'firebase'
export default {
    name: 'Navbar',
    data() {
        return {
            isLoggedIn: false,
            currentUser: false
        }
    },
    created() {
        if(firebase.auth().currentUser) {
            this.isLoggedIn = true;
            this.currentUser = firebase.auth().currentUser.email;
        }
    },
    methods: {
        logout: function(){
            firebase.auth().signOut().then(() => {
                this.$router.push('/login');
            });
        }
    }
}
</script>

<style>
ul.right {
    padding-top: 18px;
}
.navbar nav{
    padding: 0 20px;
}

div#app {
    padding-top: 150px;
}

.btn-outline-primary {
    color: #408dc5;
    border-color: #408dc5;
}

.btn-outline-primary:hover {
    color: #fff;
    background-color: #408dc5;
    border-color: #408dc5;
}


</style>