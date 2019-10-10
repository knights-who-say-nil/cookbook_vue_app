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
        <img v-on:click="showRecipe(recipe)" v-bind:src="recipe.image_url">

        <div v-if="recipe === currentRecipe">
          <p>Ingredients: {{ recipe.ingredients }}</p>
          <p>Directions: {{ recipe.directions }}</p>

          <div class="edit-form-section">
            <h4>Edit Recipe</h4>

            <div>
              Title: <input type="text" v-model="recipe.title">
            </div>

            <div>
              Chef: <input type="text" v-model="recipe.chef">
            </div>

            <div>
              Prep Time: <input type="text" v-model="recipe.prep_time">
            </div>

            <div>
              Ingredients: <input type="text" v-model="recipe.ingredients">
            </div>

            <div>
              Directions: <input type="text" v-model="recipe.directions">
            </div>

            <div>
              Image URL: <input type="text" v-model="recipe.image_url">
            </div>

            <button v-on:click="updateRecipe(recipe)">Update</button>
          </div> <!-- end of .edit-form-section -->
          
          <div class="destroy-section">
            <button v-on:click="destroyRecipe(recipe)">Destroy</button>
          </div> <!-- end of .destroy-section -->
        </div>
        
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
      newRecipeImageUrl: "",
      currentRecipe: {}
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
    destroyRecipe: function(recipeObject) {
      axios
        .delete("/api/recipes/" + recipeObject.id)
        .then(response => {
          var index = this.recipes.indexOf(recipeObject);
          this.recipes.splice(index, 1);
        });
    },
    updateRecipe: function(recipeObject) {
      var clientParams = {
        title: recipeObject.title,
        chef: recipeObject.chef,
        prep_time: recipeObject.prep_time,
        ingredients: recipeObject.ingredients,
        directions: recipeObject.directions,
        image_url: recipeObject.image_url
      };

      axios
        .patch("/api/recipes/" + recipeObject.id, clientParams)
        .then(response => {
          console.log("Success", response.data);
        });
    },
    showRecipe: function(recipeObject) {
      if (this.currentRecipe !== recipeObject) {
        this.currentRecipe = recipeObject;
      } else {
        this.currentRecipe = {};
      }
    },
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