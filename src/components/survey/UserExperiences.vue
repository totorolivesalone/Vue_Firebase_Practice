<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences">Load Submitted Experiences</base-button>
      </div>
      <p v-if="isLoading">Loading...</p>
      <p v-else-if="!isLoading && error">
        {{ error }}
      </p>
      <p v-else-if="!isLoading && (!results || results.length ===0)">No stored experiences found. Add experiences </p>
      <ul v-else>
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from './SurveyResult.vue';
import { API_CONFIG } from '../../config';

export default {
  components: {
    SurveyResult,
  },
  data(){
    return{
      results:[],
      isLoading:false,
      error:null
    }
  },
  methods:{
    loadExperiences(){
      this.isLoading=true;
      fetch(`${API_CONFIG.BASE_URL}${API_CONFIG.ENDPOINTS.SURVEYS}`,{
        method:'GET',}).then((response)=>{
          if(response.ok){
           return response.json();
          }
        }).then((data)=>{
          this.isLoading=false;
          const results=[];
          for (const id in data){
            results.push({id: id, name:data[id].name, rating:data[id].rating})
          }
          this.results=results;
        }).catch((error)=>{
          console.log(error);
          this.isLoading=false;
          this.error ='Failed to fetch data - please try again later'

        })
        

    }

  },mounted(){
    this.loadExperiences()
  }
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>