<template>
  <div class="autocomplete">

    <div>

      <h3 style="color: purple">How it works</h3>
      <br>
      <h5 style="color: dark-blue">1. Start by typing in your favourite movie</h5>
      <h5 style="color: dark-blue">2. Corresponding movie titles will pop-up below</h5>
      <h5 style="color: dark-blue">3. Select your favourite movie option & 'Generate Recommendations!'</h5>
      <br>
      
    </div>

    <form method="post" v-on:submit.prevent>

    <input id = "inputBox"
      v-model="movieTitle"
      @input="onChange"
      type="text"
    />
    <ul
      v-show="isOpen"
      class="autocomplete-results"
    >
      <li
        v-for="(result, i) in results"
        :key="i"
        @click = "setResult(result)"
        class="autocomplete-result"
      >
        {{ result }}
      </li>
    </ul>

    
    </form>
    <br/>
    <div> 
      <button type="submit" v-on:click="submit()" class = "button">Generate Recommendations!</button>
      <button type="submit" v-on:click="clear()" class = "button">Clear</button>
    </div>

    <br/>

    <h2 v-if="this.recommendations.length > 1"> Your Recommendations </h2>

      <div v-if="this.recommendations.length > 1" class="container" style="max-width: 1200px; margin-top:20px;">
        <div class="col-lg-12">
              <div class="row">

                <div v-for="movie in this.recommendations" :key="movie" class="movie-card col-md-2">

                        <img v-if="movie['poster']=='True'" :src="getImgUrl(movie['movieId'])" v-bind:alt="pic">

                </div>
              </div>
        </div>
      </div>
    <h2 v-if="this.recommendations.length === 1"> Please search for another movie as this one is not in our database </h2>
  </div>
</template>

<script>

import $ from 'jquery';
import axios from 'axios';
import all_movies_json from './json/movies.json';

let titles_arr = all_movies_json.map(a => a.title);
console.log(titles_arr)

export default {
  name: 'SearchAutocomplete',
  props: {
    items: {
      type: Array,
      required: false,
      default: () => titles_arr,//[]
    },
  },
  data() {
    return {
      movieTitle: '',
      results: [],
      isOpen: false,
      recMovie : '',
      api_recommendations: [],
      recommendations:[],
      movie:{}
    };
  },
  mounted() {
    document.addEventListener('click', this.handleClickOutside);
  },
  destroyed() {
    document.removeEventListener('click', this.handleClickOutside);
  },
  methods: {
    submit () {
      var self = this;
      self.recommendations = [];
      axios.post('http://0.0.0.0:80/recms', {
        movie_title: this.movieTitle
      })
      .then(function(response){

        var api_recommendations = response.data.rec_movie                           //api_recommendations is an array containing a list of titles
        var found_movie = [];                                                       //dummy variable to store a matched movie
        
        $.each(api_recommendations,function(movie_title){                           //movie_title is just an index 0,1,2..
                    
            found_movie = all_movies_json.filter((obj) => obj.title === api_recommendations[movie_title]);
            self.recommendations.push(found_movie[0])

        });
      })
    },

    clear () {
      this.movieTitle = ''
      this.api_recommendations = []
      this.recommendations = []
    },

    getImgUrl(imgName) {
            return `/posters/${imgName}.jpg`
    },
    handleClickOutside(event){
      if (!this.$el.contains(event.target)) {
        this.isOpen = false;
      }
    },
    setResult(result) {
      this.movieTitle = result;
      this.isOpen = false;
    },
    filterResults() {
      this.results = this.items.filter(item => item.toLowerCase().indexOf(this.movieTitle.toLowerCase()) > -1);
    },
    onChange() {
      this.filterResults();
      this.isOpen = true;
    }
  },
}
</script>

<style>
body{
    font-weight: normal;
    font-style: normal;
    font-family: 'Avenir',Helvetica,Arial,sans-serif;
    background-color: lightgrey;
    color: black;
  
  }

  .movie-card{
      margin-top:0.3rem;
  
  }
  .movie-no-image{
      border: 2px solid black;
      border-radius: 10px;
      width: 185px;
      height: 278px;
      padding: 10px;
      font-size: 1.5rem;
      overflow-wrap: break-word;
  }
  #inputBox {
    border: 2px solid darkblue;
  }

  .button{
    margin: 3px;
    border-radius: 5px;
  }

  .autocomplete {
    position: relative;
    /* border: 1px solid #eeeeee; */
  }


  .autocomplete-results {
    padding: 0;
    margin: 0;
    border: 1px solid #eeeeee;
    height: 120px;
    min-height: 1em;
    max-height: 6em;
    overflow: auto;
  }

  .autocomplete-result {
    list-style: none;
    text-align: left;
    padding: 4px 2px;
    cursor: pointer;
  }

  .autocomplete-result:hover {
    background-color: lightsteelblue;
    color: white;
  }
</style>