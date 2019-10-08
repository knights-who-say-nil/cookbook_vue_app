<template>
  <div class="home">
    <div>
      <h1>New Recipe</h1>
      <ul>
        <li v-for="error in errors">{{ error }}</li>
      </ul>
      <button v-on:click="createRecipe()">Create</button>
    </div>

    <h1>All Recipes</h1>

    <div v-for="recipe in recipes">
      <h2>{{ recipe.title }}</h2>
      <p>Ingredients: {{ recipe.ingredients }}</p>
      <p>Directions: {{ recipe.directions }}</p>
      
      <img v-bind:src="recipe.image_url">
      <br>
    </div>

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
      errors: []
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
        title: "example Title 3",
        chef: "example Chef",
        prep_time: "example Prep Time",
        ingredients: "example Ingredients", 
        directions: "example Directions",
        image_url: "example Image URL"
      };

      axios
        .post("/api/recipes", clientParams)
        .then(response => {
          console.log("success", response.data);
          this.recipes.push(response.data);
          this.errors = [];
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    }
  }
};
</script>