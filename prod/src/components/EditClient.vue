<template>
    <div v-if="client">
         <div class="text-center">
    <h2 class="card-title">Modifier client</h2> </div>
    <h4>Modifier {{ client.title }}</h4>
    <form @submit.prevent="EditClient">
            <div class="field title">
                <label for="tilte">Prénom et nom</label>
                <input type="text" name="title" v-model="client.title">
            </div>
            <div v-for="(ing, index) in client.ingredients" :key="index" class="field">
                <label for="ingredient">Ingredients:</label>
                <input type="text" name="ingredient" v-model="client.ingredients[index]">
                <i class="material-icons delete" @click="deleteIng(ing)">delete</i>
            </div>
           
            <div class="field add-ingredients">
                <label for="add-ingredient">Ajouter un article</label>
                <input v-model="another" type="text"/>
                <button class="btn blue" type="submit" @click.prevent="addIng()">Ajouter</button>
            </div>
            <div class="field center-align">
                <p v-if="feedback" class="red-text">{{ feedback  }}</p>
                <button class="btn blue">Enregistrer modifications</button>
                <router-link to="/" class="btn grey">Annuler</router-link>

            </div>
        </form>
    </div>
</template>
<script>
import db from '@/firebase/init'
import slugify from 'slugify'
export default {
    name: 'EditClient',
    data(){
        return{
            client: null,
            another: null,
            feedback: null
        }
    },
    methods:{
        EditClient(){
            if (this.client.title){
                this.feedback = null
                //create slug
                this.client.slug = slugify(this.client.title, {
                    replacement: '-',
                    remove: /[$*_+~.()'"!\-:@]/g,
                    lower: true
                })
                db.collection('clients').doc(this.client.id).update({
                   title: this.client.title,
                   ingredients: this.client.ingredients,
                   slug: this.client.slug
                }).then(() => {
                    this.$router.push({ name: 'Index'})
                }).catch(err => {
                    console.log(err)
                })
            } else {
                this.feedback = 'you must blblbla title'
            }
        },
        addIng(){
            if(this.another){
                this.client.ingredients.push(this.another)
                this.another = null
                this.feedback = null

            } else {
                this.feedback = 'you blablabla'
            }
        },
        deleteIng(ing){
            this.client.ingredients = this.client.ingredients.filter(ingredient => {
              return ingredient != ing  
            })
        }
    },
    created(){
        let ref = db.collection('clients').where('slug', '==', this.$route.params.client_slug)
        ref.get().then(snapshot => {
            snapshot.forEach(doc =>{
               this.client = doc.data()
               this.client.id = doc.id 
            })
        })
    }
}
</script>

<style>

.edit-client{
    margin-top: 60px;
    padding: 20px;
    max-width: 500px;
    background-color: white;
}
.editclient h2{
    font-size: 2em;
    margin: 20px auto;
}
.edit-client .field{
    margin: 20px auto;
    position: relative;
}

.edit-client .delete{
    position: absolute;
    right: 0;
    bottom: 16px;
    color: #aaa;
    font-size: 1.4em;
    cursor: pointer;

}

</style>