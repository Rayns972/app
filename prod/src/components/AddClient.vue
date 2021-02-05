<template>
  <div>
    <div class="text-center">
      <h2 class="card-title">Ajouter un nouveau client</h2>
    </div>
    <div class="card mx-auto add" style="width: 600px">
      <form class="card-body" @submit.prevent="AddClient">
        <form>
          <div class="form-row">
            <div class="form-group col-md-6">
              <label for="inputName">Prénom</label>
              <input
                type="text"
                class="form-control"
                name="title"
                v-model="title"
              />
            </div>
            <div class="form-group col-md-6">
              <label for="inputprenom">Nom</label>
              <input
                type="text"
                class="form-control"
                name="nom"
                v-model="nom"
              />
            </div>
          </div>

          <div class="form-group">
            <label for="inputTel">Fixe</label>
            <input
              type="text"
              class="form-control"
              name="fixe"
              v-model="fixe"
            />
          </div>

          <div class="form-group">
            <label for="inputTel">Portable</label>
            <input
              type="text"
              class="form-control"
              name="portable"
              v-model="portable"
            />
          </div>

          <div class="form-group">
            <label for="inputEmail4">Email</label>
            <input
              type="email"
              class="form-control"
              name="email"
              v-model="email"
            />
          </div>

          <div class="form-group">
            <label for="inputAddress">Adresse</label>
            <input
              type="text"
              class="form-control"
              name="adresse"
              v-model="adresse"
            />
          </div>

          <div class="form-row">
            <div class="form-group col-md-10">
              <label for="inputCity">Ville</label>
              <input
                type="text"
                class="form-control"
                name="ville"
                v-model="ville"
              />
            </div>

            <div class="form-group col-md-2">
              <label for="inputZip">CP</label>
              <input type="text" class="form-control" name="cp" v-model="cp" />
            </div>
          </div>
        </form>

        <!-- quantite -->
        <div class="form-row">
          <div class="form-group col-md-3">
            <label for="qte">Quantité</label>

            <input
              v-model="qty"
              class="form-control"
              type="number"
              value="1"
              min="1"
              max="1000"
            />
          </div>

          <!-- article -->
          <div class="form-group col-md-6">
            <label for="article">Choisir article</label>
            <select
              v-model="article"
              name="article"
              class="custom-select"
              aria-label="Default select example"
            >
              <option
                v-for="article in articlesAutoComplete"
                :key="article.id"
                :value="article.titre"
              >
                {{ article.titre }}
              </option>
            </select>
          </div>

          <!-- la taille -->
          <div class="form-group col-md-3">
            <label for="taille">Taille</label>
            <input
              type="text"
              class="form-control"
              name="Taille"
              v-model="taille"
            />
          </div>
        </div>
        <button class="btn btn-primary" type="submit" @click.prevent="addIng()">
          Ajouter
        </button>

        <ul>
          <li v-for="(article, index, key) in articles" :key="key">
            qty: {{ article.qty }} <br />
            La taille: {{ article.taille }} <br />
            article type: {{ article.article }}
          </li>
        </ul>
        <!-- <div v-for="(ing, index) in ingredients" :key="index" class="field">
                <label for="ingredient">Articles :</label>
                <input type="text" name="ingredient" v-model="ingredients[index]">
                <i class="material-icons delete" @click="deleteIng(ing)">delete</i>
            </div> -->

        <!-- <div class="field add-ingredients">
                <label for="add-ingredient">Ajouter articles</label>
                
                <input v-model="another" type="text"/>
                
            </div> -->
        <div class="field center-align">
          <p v-if="feedback" class="red-text">{{ feedback }}</p>

          <button type="submit" class="btn btn-primary">Enregistrer</button>
          <router-link to="/" class="btn btn-primary">Annuler</router-link>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import db from "@/firebase/init";
import slugify from "slugify";

export default {
  name: "AddClient",
  data() {
    return {
      value: 1,
      articles: [],
      articlesAutoComplete: [],
      article: null,
      title: null,
      nom: null,
      qty: null,
      fixe: null,
      portable: null,
      email: null,
      adresse: null,
      ville: null,
      cp: null,
      another: null,
      feedback: null,
      slug: null,
      titre: null,
      taille: null,
    };
  },
  methods: {
    AddClient() {
      if (this.title) {
        this.feedback = null;
        //create slug
        this.slug = slugify(this.title, {
          replacement: "-",
          remove: /[$*_+~.()'"!\-:@]/g,
          lower: true,
        });
        db.collection("clients")
          .add({
            title: this.title,
            nom: this.nom,
            fixe: this.fixe,
            portable: this.portable,
            email: this.email,
            adresse: this.adresse,
            ville: this.ville,
            cp: this.cp,
            taille: this.taille,
            qty: this.qty,
            articles: this.articles,
            slug: this.slug,
            completed: false,
            article: this.article,
            createdAt: new Date(),
          })
          .then(() => {
            this.$router.push({ name: "Index" });
          })
          .catch((err) => {
            console.log(err);
          });
      } else {
        this.feedback = "Il manque des informations";
      }
    },

    addIng() {
      if (this.qty && this.taille && this.article) {
        const article = {
          qty: this.qty,
          taille: this.taille,
          article: this.article,
        };
        this.articles.push(article);
        // a = null;
      } else {
        this.feedback = "vous devez sélectionner des articles";
      }
    },

    deleteIng(ing) {
      this.ingredients = this.ingredients.filter((ingredient) => {
        return ingredient != ing;
      });
    },
  },
  created() {
    db.collection("articles")
      .get()
      .then((snapshot) => {
        snapshot.forEach((doc) => {
          let article = doc.data();
          console.log('article', article)
          article.id = doc.id;
          this.articlesAutoComplete.push(article);
        });
      });
  },
};
</script>

<style>
body {
  background-color: #3f8dc5;
  font-family: "Ubuntu", sans-serif;
}

.add-client {
  margin-top: 0px;
  padding: 20px;
  max-width: 500px;
  background-color: white;
}
.add-client h2 {
  font-size: 2em;
  margin: 20px auto;
}
.add-client .field {
  margin: 20px auto;
  position: relative;
}

.add-client .delete {
  position: absolute;
  right: 0;
  bottom: 16px;
  color: #aaa;
  font-size: 1.4em;
  cursor: pointer;
}

.md-menu-content-container.md-scrollbar.md-theme-default {
  background-color: #d7eeff;
}

.add-client {
  margin-top: 60px;
  padding: 20px;
  max-width: 600px;
}

.md-list {
  width: 320px;
  max-width: 100%;
  display: inline-block;
  vertical-align: top;
  border: 1px solid rgba(#000, 0.12);
}

.text-center {
  text-align: center !important;
  color: white;
  padding-bottom: 15px;
}
.red-text {
  color: red;
}
</style>