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
          <div v-on:click="showMain(i)">
            <span class="arrow" v-bind:class="{ rotate: showMainDescription[i] }">
              ► <!--{{showMainDescription[i] ? "▼" : "►"}}-->
            </span>
            <span data-tooltip="description" class="title">
              &nbsp;{{broadTrait.title}}&nbsp;
            </span>
            <span class="symbole">{{avarage(i,symbole)}}</span>
          </div>
          <b v-on:click="showMain(i)">{{broadTrait.subtitle}}</b>
        </h2>
        <div class="mainDescription" v-bind:class="{ isShowing: showMainDescription[i] }">
          {{broadTrait.description}}. <br>
          {{broadTrait.highScore}} <br>
          {{broadTrait.lowScore}}
        </div>

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
                <span class="arrow"  v-bind:class="{ rotate: showDescription[i][item] }"> 
                  ► <!--{{showDescription[i][item] ? "▼" : "►"}}-->
                </span> 
                <span>&nbsp;{{broadTrait.facets[item].title}}</span>
                <!--<span class="symbole">{{scores[i][item] > 2 ? symbole.high : scores[i][item] < 2 ?  symbole.low : symbole.middle}}</span>-->
              </div>
              <div class="slider">
                <!--<input type='range' min="1" max="3" value="2" v-model="scores[i][item]"/>-->
                
                <div class="custom-radios">
                  <div>
                    <input type="radio" v-bind:id="broadTrait.facets[item].title + '-1'"
                      v-bind:name="broadTrait.facets[item].title" v-bind:value="1" v-model="scores[i][item]">
                    <label v-bind:for="broadTrait.facets[item].title + '-1'">
                      <span>
                        ⇓
                      </span>
                    </label>
                  </div>

                  <div>
                    <input type="radio" v-bind:id="broadTrait.facets[item].title + '-2'" 
                      v-bind:name="broadTrait.facets[item].title" v-model="scores[i][item]" v-bind:value="2">
                    <label v-bind:for="broadTrait.facets[item].title + '-2'">
                      <span>
                        =
                      </span>
                    </label>
                  </div>

                  <div>
                    <input type="radio" v-bind:id="broadTrait.facets[item].title + '-3'" 
                      v-bind:name="broadTrait.facets[item].title" v-model="scores[i][item]" v-bind:value="3">
                    <label v-bind:for="broadTrait.facets[item].title + '-3'">
                      <span>
                        ⇑
                      </span>
                    </label>
                  </div>
                </div>
                
              </div>
            </div>
            <div class="description" v-bind:class="{ isShowing: showDescription[i][item] }">
              {{broadTrait.facets[item].description}}
            </div>
          </div>
        </div>

        <div class="summary">
          {{broadTrait.approach}}:
          <ul>
            <li v-for="(facet, index) in broadTrait.facets" v-if="(facet.Score['middle'] != null && scores[i][index] == 2) || scores[i][index] != 2"><!--v-if="scores[i][index] != 2"-->
              {{scores[i][index] > 2 ? facet.Score["high"] : scores[i][index] < 2 ?  facet.Score["low"] : facet.Score["middle"]}}
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
        [2,1,3,2,2,2],
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
      showMainDescription: [false,false,false,false,false],
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
    showMain: function(i) {
      Vue.set(this.showMainDescription, i, !this.showMainDescription[i])
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

    .arrow {
      font-size: 15px;
      transition: transform .3s ease;
    }

    .rotate {
      transform: rotateZ(90deg);
    }
    
    .button {
      position: absolute;
      top: 0;
      right: 0;
      padding: 0;
    }
  }
  
  h2 {
    margin: 5px 0 5px 0;
    line-height: 20px;

    div {
      display: flex;
    }
    
    .title {
      cursor: pointer;  
      font-size: 22px;
    }
    
    .symbole {
      font-size: 20px;
    }

    b {
      font-size: 15px;
      cursor: pointer; 
    }
  }

  .mainDescription {
    padding-bottom: 0;
    max-height: 0;
    overflow: hidden;
    transition: all .3s ease;
  }
    
  .mainDescription.isShowing {
    padding-bottom: 5px;
    max-height: 200px;
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
      padding-bottom: 0;
      max-height: 0;
      overflow: hidden;
      transition: all .3s ease;
    }
    
    .description.isShowing {
      padding-bottom: 5px;
      max-height: 300px;
    }
    
    .title{
      padding: 5px 0 5px 0;
      cursor: pointer;
      display: inline-flex;
      align-items: center;

      .arrow {
        font-size: 12px;
        transition: transform .3s ease;
      }

      .rotate {
        transform: rotateZ(90deg);
      }
      
      .symbole{
        font-size: 14px;
      }
    }
    
    .slider{
      padding: 5px 0;
      max-width: 100px;
      width: 100px;
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


// Radio buttons
$color-1: #ffcc00;
$color-2: #ff9b00;
$color-3: #ff6600;

.custom-radios {
  width: 100%;
  height: 24px;
  
  div {
    display: inline-block;
    width: calc(100% / 3);;
    height: 100%;
    font-size: 15px;
    
    &:nth-child(1) input[type="radio"] + label{
      background-color: $color-1;
    } 
    
    &:nth-child(2) input[type="radio"] + label{
      background-color: $color-2;
    } 
    
    &:nth-child(3) input[type="radio"] + label{
      background-color: $color-3;
    } 
    
    &:nth-child(2) {
     margin: 0 -5px;
    }
  }

  input[type="radio"] {
    display: none;

    + label {
      color: white;
      font-family: Arial, sans-serif;
      display: inline-block;
      width: 100%;
      height: 100%;
      cursor: pointer;
      border-radius: 0;
      background-repeat: no-repeat;
      background-position: center;
      text-align: center;
      transform: scale(1, 0.5);
      transition: all .1s ease;
      vertical-align: middle;
      line-height: 22px;

      position:relative;z-index:50;
      &:before{
          position:absolute;
          content:'';
          top:-10px;
          right:0;
          left:0;
          bottom:-10px;
          z-index:40;
      }

      span {
        opacity: 0;
        vertical-align: middle;
        transition: opacity .3s ease;
      }
    }

    &:checked + label span {
      opacity: 1;
    }
    
    &:checked + label{
      transform: scale(1, 1);
      border-radius: 5px;
    }
  }
}
</style>
