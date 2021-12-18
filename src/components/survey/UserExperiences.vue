<template>
  <div class="loading-container">
    <div v-if="isLoading" class="loading">
      <span></span>
    </div>
  </div>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences">Load Submitted Experiences</base-button>
      </div>
      <p v-if="!isLoading && error">{{ error }}</p>
      <p v-else-if="!isLoading && (!results || results.length === 0)">No stored experiences found. Start adding some survey result first </p>
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

export default {
  components: {
    SurveyResult,
  },
  data() {
    return {
      results: [],
      isLoading: false,
      error: null
    }
  },
  methods: {
    loadExperiences(){
      this.isLoading = true;
      this.error = null
      fetch('https://vue-http-demo-977d6-default-rtdb.firebaseio.com/surveys.json')
      .then((response) => {
        if (response.ok) {
          return response.json();
        }
      })
      .then((data) => {
        this.isLoading = false;
        const results = [];
        for (const id in data) {
          results.push({
            id: id,
            name: data[id].name,
            rating: data[id].rating,
          })
        }
        this.results = results;
      })
      .catch((error) => {
        console.log(error);
        this.isLoading = false;
        this.error = 'Failed to fetch data - please try again later'
      })
    }
  },
  mounted() {
    this.loadExperiences();
  }
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
.loading-container{
  
  display: flex;
  justify-content: center;
  align-items: center;
}

.loading {
    z-index: 101;

    display: flex;
    justify-content: center;
    align-items: center;
}
@keyframes spin {
  to {
      transform: rotateZ(360deg);
  }
}
.loading span {
        display: block;
        width: 60px;
        height: 60px;
        margin: 0 auto;
        border: 3px solid transparent;
        border-top-color: black;
        border-bottom-color: black;
        border-radius: 50%;
        animation: spin ease 1000ms infinite;
}

</style>