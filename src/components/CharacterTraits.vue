<template>
  <div class="new">
    <div class="header">
      <h1>Big Five personality traits</h1>
      <button class="button" type="button" v-on:click="save">Save in browser</button>
      <button class="button" type="button" v-on:click="reset">Reset</button>
      <button class="button" type="button" v-on:click="asImage">Save as image</button>
    </div>
    <div class="traits" id="capture">
      <div class="broad-trait" v-for="(broadTrait, key, i) in broadTraits">
        <h2>
          <span data-tooltip="description">{{broadTrait.title}}</span>
          <span class="symbole">{{avarage(i,symbole)}}</span>
        </h2>
        <button class="button" type="button" v-on:click="minimize(i)">
          {{minimized[i] ? "▼" : "▲"}}
        </button>
        
        <div class="minimized" v-show="minimized[i]">
          <div v-for="(category, key) in broadTrait.facetCategories">
            <h4 class="category">{{key}}:</h4>
            <div class="facet" v-for="item in category">
              {{broadTrait.facets[item].title}} 
              <span class="symbole">{{scores[i][item] > 2 ? symbole.high : scores[i][item] < 2 ?  symbole.low : symbole.middle}}</span>
            </div>
          </div>
        </div>

        <div v-for="(category, key) in broadTrait.facetCategories" v-show="!minimized[i]">
          <h4 class="category">{{key}}</h4>
          <div class="facet" v-for="item in category">
            <div class="scoring">
              <div class="title" v-on:click="show(i, item)">
              {{broadTrait.facets[item].title}} 
                <span class="symbole">{{scores[i][item] > 2 ? symbole.high : scores[i][item] < 2 ?  symbole.low : symbole.middle}}</span>
              </div>
              <div class="slider">
                <input type='range' min="1" max="3" value="2" v-model="scores[i][item]"/>
              </div>
            </div>
            <div class="description" v-if="showDescription[i][item]">
              {{broadTrait.facets[item].description}}
            </div>
          </div>
        </div>

        <div class="summary">
          {{broadTrait.approach}}:
          <ul>
            <li v-for="(facet, index) in broadTrait.facets" v-if="scores[i][index] != 2">
              {{scores[i][index] > 2 ? facet.Score["high"] : scores[i][index] < 2 ?  facet.Score["low"] : null}}
            </li>
          </ul>
          <p>
            Can be perceived as: 
            <span class="negetivePerception">
              {{avarage(i, broadTrait.negetivePerception)}}
            </span>
          </p>
          <p>
            Traits: 
            <span class="trait" v-for="(facet, index) in broadTrait.facets" v-if="scores[i][index] != 2">
              {{scores[i][index] > 2 ? facet.strong : scores[i][index] < 2 ?  facet.weak : null}}
            </span>
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import traits from '../assets/personalityTraits'
import html2canvas from 'html2canvas';
import Vue from 'vue'

export default {
  name: 'CharacterTraits',
  beforeMount: function () {
    if(localStorage.getItem("scores")){
      this.scores = JSON.parse(localStorage.getItem("scores"));        
    }
  },
  data () {
    return {
      broadTraits: traits,
      scores: [
        [2,2,2,2,2,2],
        [2,2,2,2,2,2],
        [2,2,2,2,2,2],
        [2,2,2,2,2,2],
        [2,2,2,2,2,2]
      ],
      symbole: {
        high : "⇑",
        middle : "=",
        low : "⇓"
      },
      showDescription: [
        [false,false,false,false,false,false],
        [false,false,false,false,false,false],
        [false,false,false,false,false,false],
        [false,false,false,false,false,false],
        [false,false,false,false,false,false]
      ],
      minimized: [false,false,false,false,false]
    }
  },
  computed: {},
  methods: {
    avarage: function (index, neg){
      var total = this.scores[index].reduce((a, b) => parseInt(a) + parseInt(b), 0);
      var avarage = total / 6;
      
      return avarage > 2.4 ? neg["high"] : avarage < 1.6 ?  neg["low"] : neg["middle"];
    },
    show: function(i, item) {
      Vue.set(this.showDescription[i], item, !this.showDescription[i][item])
    },
    save: function(){
      localStorage.setItem("scores", JSON.stringify(this.scores));
    },
    reset: function(){
      this.scores = [
        [2,2,2,2,2,2],
        [2,2,2,2,2,2],
        [2,2,2,2,2,2],
        [2,2,2,2,2,2],
        [2,2,2,2,2,2]
      ];
      localStorage.setItem("scores", JSON.stringify(this.scores));
    },
    asImage: function(){
      var temp = this.minimized;
      this.minimized = [true,true,true,true,true];
      function makeImage() {
        html2canvas(document.querySelector("#capture")).then(canvas => {
          var image = canvas.toDataURL("image/png").replace("image/png", "image/octet-stream");  
          window.location.href=image;
          this.minimized = temp;
        });
      };
      var makeImage = makeImage.bind(this);
      
      setTimeout(makeImage, 500);
    },
    minimize: function(index){
      Vue.set(this.minimized, index, !this.minimized[index])
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.header {
  padding: 5px;
  
  h1 {
    margin: 10px 0;
  }
  
  .button {
    background-color: #4CAF50;
    border: none;
    color: white;
    padding: 5px 20px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 16px;
  }
}

.traits {
  display: flex;
  flex-wrap: wrap;
  
  .broad-trait {
    background: #dedede;
    width: 275px;
    min-width:250px;
    padding: 5px;
    margin-bottom: 5px;
    margin-left: 5px;
    display: flex;
    flex-direction: column;
    position: relative;
    
    .button {
      position: absolute;
      top: 0;
      right: 0;
      padding: 0;
    }
  }
  
  h2 {
    margin: 5px 0 5px 0;
    
    .symbole {
      font-size: 20px;
    }
  }
  
  .category{
    margin: 5px 0;
  }
  
  .traitRange{
    padding: 5px 0 5px 0;
  }
  
  .minimized{   
    .category{
      display: unset;
    }
    
    .facet{
      display: inline-block;
      margin-right: 5px;
      border-radius: 3px;
      padding: 0 5px 1px 5px;
      line-height: 22px;
    }
  }
  
  .facet{
    background: #ececec;
    margin-bottom: 5px;
    padding: 0 5px;
    
    .scoring {
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    
    .description {
      padding-bottom: 5px;
    }
    
    .title{
      padding: 5px 0 5px 0;
      cursor: pointer;
      
      .symbole{
        font-size: 14px;
      }
    }
    
    .slider{
      padding-top: 5px;
      max-width: 70px;
    }
  }
  
  .summary {
    background: white;
    padding: 5px;
    flex: 1;
    
    ul {
      margin: 0;
      padding-left: 30px;
    }
    
    p {
      margin: 20px 0 0 0;
      line-height: 20px;
      
      .trait {
        background: #dadada;
        border-radius: 3px;
        padding: 0 5px 1px 5px;
        margin-right: 2px;
        line-height: 22px;
      }
      
      .negetivePerception{
        font-weight: bold;
      }
    }
  }
}

//https://css-tricks.com/sliding-nightmare-understanding-range-input/?utm_source=CSS-Weekly&utm_campaign=Issue-296&utm_medium=email
//Source: https://codepen.io/thebabydino/pen/qVQLzm
$track-w: 100%;
$track-h: 0.5em;
$thumb-d: 1.5em;
$thumb-wd: 2em;

@mixin track() {
	box-sizing: border-box;
	border: none;
	width: $track-w; height: $track-h;
	background: #ccc;
}

@mixin thumb() {
	box-sizing: border-box;
	border: none;
	width: 33.33%; 
  height: $thumb-d;
	border-radius: 5px;
	background: #f90;
}

[type='range'] {
	&, &::-webkit-slider-thumb {
		-webkit-appearance: none;
	}
	
	margin: 0;
	padding: 0;
	width: $track-w; height: $thumb-d;
	background: transparent;
	font: 1em/1 arial, sans-serif;
	
	&::-webkit-slider-runnable-track {
		@include track
	}
	&::-moz-range-track { @include track }
	&::-ms-track { @include track }
	
	&::-webkit-slider-thumb {
		margin-top: .5*($track-h - $thumb-d);
		@include thumb
	}
	&::-moz-range-thumb { @include thumb }
	&::-ms-thumb {
		margin-top: 0;
		@include thumb
	}
	
	&::-ms-tooltip { display: none }
}
</style>
