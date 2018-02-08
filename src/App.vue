<template>
  <div id="app">
    <CharacterTraits/>
    <h2>References</h2>
    <ul>
      <li><a href="https://en.wikipedia.org/wiki/Big_Five_personality_traits" target="_blank">
          Big Five personality traits
        </a></li>
      <li><a href="https://en.wikipedia.org/wiki/Facet_%28psychology%29" target="_blank">
          Facet (psychology)
        </a></li>
      <li><a href="http://www.psychometric-success.com/personality-tests/personality-tests-big-5-aspects.htm" target="_blank">
          The ‘Big 5’ Aspects of Personality
        </a></li>
      <li><a href="https://www.mindtools.com/pages/article/newCDV_22.htm" target="_blank">
          The Big Five Personality Traits Model and Test
        </a></li>
      <li><a href="https://www.ets.org/s/workforce_readiness/pdf/21332_big_5.pdf" target="_blank">
          [PDF] Facets of the Big Five
        </a></li>
      <li><a href="http://www.personal.psu.edu/faculty/j/5/j5j/IPIPNEOdescriptions.html" target="_blank">
          Descriptions Used in IPIP-NEO Narrative Report
        </a></li>
    </ul>
  </div>
</template>

<script>
import CharacterTraits from './components/CharacterTraits'

export default {
  name: 'App',
  components: {
    CharacterTraits
  }
}
</script>

<style>
#app {
  /*font-family: 'Avenir', Helvetica, Arial, sans-serif;*/
}
</style>
