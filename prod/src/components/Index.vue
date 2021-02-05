<template>

  <div class="index container">


    <div class="card mx-auto add" style="width:300px" v-for="client in clients" :key="client.id">
      <div class="card-body">
        <i class="material-icons delete" @click="deleteClient(client.id)">delete</i>
        <h2 class="indigo-text">{{ client.title }} {{client.nom}}</h2>
        <ul class="ingredients">
         <span>{{ client.articles }}</span>
          <div class="collection" v-for="(ing, key, index) in client.ingredients" :key="index">
     
 
    
    
  </div>
  <div class="collection" v-for="(qty, key, index) in client.qty" :key="index">
     
 
     <p>
      <label>
        <input type="checkbox" class="filled-in"  v-model="client.completed"/>
        <span :class="{ completed : client.completed }" >{{ qty }}</span>
      </label>
    </p>
    
  </div>

        </ul>
      </div>
      <span class="btn-floating btn-large halfway-fab blue darken-2">
        <router-link :to="{ name: 'EditClient', params: {client_slug: client.slug}}">
          <i class="material-icons edit">edit</i>
        </router-link>
      </span>
    </div>


        
  </div>
  
</template>

<script>
import db from '@/firebase/init'

export default {
  name: 'Index',
  data () {
    return {
      clients: []
    }
  },
  firestore() {
    return {
      clients: dbCollection.orderBy('createdAt', 'desc')
    }
  },
  
methods: {
  deleteClient(id){
    if(confirm('Êtes-vous sur de vouloir supprimer ce client ?')){
//effacer de firestore
    db.collection('clients').doc(id).delete()
    .then(() => {
      this.clients = this.clients.filter(client => {
        return client.id != id
      })
    })
    }
    
  }
},
  created(){
    //récuperer data de firestore
    db.collection('clients').get()
    .then(snapshot => {
      snapshot.forEach(doc => {
        let client = doc.data()
        client.id = doc.id
        this.clients.push(client)
        
      })
    })
  },

  

  


  
}







</script>



<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.completed {
  text-decoration: line-through;
  color: grey;
}

.collection {
    margin: -0.7rem 0 1rem 0;
    border: 1px solid #e0e0e0;
    border-radius: 10px;
    overflow: hidden;
    position: relative;
}

 body {
        background-color: #3f8dc5;
        font-family: 'Ubuntu', sans-serif;
    }
.index{
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  grid-gap: 30px;
  margin-top: 60px;
}
.index h2{
  font-size: 1.8em;
  text-align: center;
  margin-top: 0;
  color: #3f8dc5 !important;

}
.index .ingredients{
  margin: 30px auto;
}
.index .ingredients li{
  display: inline-block;
}

.index .delete{
  position: absolute;
  top: 6px;
  right: 6px;
  cursor: pointer;
  color: #aaa;
  font-size: 1.4em;
}

.index .edit{
  position: absolute;
  bottom: 6px;
  right: 6px;
  cursor: pointer;
  color: #3f8dc5;
  font-size: 1.4em;
}

</style>
