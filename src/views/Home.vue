<template>
  <div class="home">
    <div class="new-form-section">
      <h1>New Recipe</h1>
      <ul>
        <li v-for="error in errors">{{ error }}</li>
      </ul>

      <div>
        Title: <input type="text" v-model="newRecipeTitle">
      </div>

      <div>
        Chef: <input type="text" v-model="newRecipeChef">
      </div>

      <div>
        Prep Time: <input type="text" v-model="newRecipePrepTime">
      </div>

      <div>
        Ingredients: <input type="text" v-model="newRecipeIngredients">
      </div>

      <div>
        Directions: <input type="text" v-model="newRecipeDirections">
      </div>

      <div>
        Image URL: <input type="text" v-model="newRecipeImageUrl">
      </div>

      <button v-on:click="createRecipe()">Create</button>
    </div> <!-- end of .new-form-section -->

    <div class="index-section">
      <h1>All Recipes</h1>

      <div v-for="recipe in recipes">
        <h2>{{ recipe.title }}</h2>
        <p>Ingredients: {{ recipe.ingredients }}</p>
        <p>Directions: {{ recipe.directions }}</p>
        
        <img v-bind:src="recipe.image_url">
        <br>
      </div>
    </div> <!-- end of .index-section -->

  </div>
</template>

<style>
img {
  width: 250px;
}
</style>

<script>
var axios = require("axios");

export default {
  data: function() {
    return {
      recipes: [],
      errors: [],
      newRecipeTitle: "",
      newRecipeChef: "",
      newRecipePrepTime: "",
      newRecipeIngredients: "",
      newRecipeDirections: "",
      newRecipeImageUrl: ""
    };
  },
  created: function() {
    axios
      .get("/api/recipes")
      .then(response => {
        this.recipes = response.data;
      });
  },
  methods: {
    createRecipe: function() {
      console.log("Create the recipe...");

      var clientParams = {
        title: this.newRecipeTitle,
        chef: this.newRecipeChef,
        prep_time: this.newRecipePrepTime,
        ingredients: this.newRecipeIngredients,
        directions: this.newRecipeDirections,
        image_url: this.newRecipeImageUrl
      };

      axios
        .post("/api/recipes", clientParams)
        .then(response => {
          console.log("success", response.data);
          this.recipes.push(response.data);
          this.errors = [];

          this.newRecipeTitle = "";
          this.newRecipeChef = "";
          this.newRecipePrepTime = "";
          this.newRecipeIngredients = "";
          this.newRecipeDirections = "";
          this.newRecipeImageUrl = "";
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    }
  }
};
</script>