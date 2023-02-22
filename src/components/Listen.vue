<template>


<div class="gradient-number">
        <span><div v-if="main_sentiment">
          {{main_sentiment["sentiment_level:"]}}
          <h3>{{main_sentiment["sentiment:"]}}</h3>
          </div></span>
        <div v-if="main_sentiment">
          <div v-if="main_sentiment['sentiment:'] == 'positive'">
            <img src="../assets/smiley.svg" class="smiley-padding" alt="smile" height="80" width="80"/>
          </div>
          <div v-if="main_sentiment['sentiment:'] == 'negative'">
            <img src="../assets/sad_icon.png" class="smiley-padding" alt="sad" height="80" width="80"/>
          </div>
          <div v-if="main_sentiment['sentiment:'] == 'neutral'">
            <img src="../assets/neutral3.png" class="smiley-padding" alt="neutral" height="80" width="80"/>
            
          </div>
        </div>
        <!-- <i class="far fa-smile yellow"></i> -->
      </div>
<!-- transcription input from voice -->
    <form>
        <div class="form-group">
          <br/>
      <button type="button" class="gradient-button" v-on:click="startListening">Listen
        <div v-if="listening">
          <i class="fa fa-spinner fa-spin"></i>
        </div>
      </button>
      <br/><br/>
      
      
          <textarea class="form-control" id="exampleFormControlTextarea1" rows="5" v-model="transcript"></textarea>
        </div>
          <div style="display: flex; justify-content: space-between;">
      <!-- <button type="submit" class="btn btn-primary" style="background: linear-gradient(to right, #00b4db, #0083b0);">Submit</button> -->
      <div v-if="transcript">
        <button @click="getSuggession" class="getsuggession-button" type="button">Get Suggession
        <div v-if="suggession_processing">
          <i class="fa fa-spinner fa-spin"></i>
        </div>
        </button>
      
        
        
        
        <button type="button" class="autocorrect-button" v-on:click="autocorrect">Autocorrect 
          <div v-if="autocorrect_processing == true">
            <i class="fa fa-spinner fa-spin"></i>
          </div>
        </button>
      </div>
    </div>
      </form>
      <br/><br/>

<!--container for sales suggessions and sentiment analysis-->
<div class="container">
        <div class="row">
          <div class="col-sm-6 col-md-6">
          <!--row split for summary and suggestions-->
            <div class="suggestions-container">
              <h2>Live Suggestions</h2>
              <!-- <ul>
                <li><strong>{{this.main_suggession}}</strong></li>
                
              </ul> -->
              <div v-if="suggession_processing">
                <i class="fa fa-spinner fa-spin"></i>
              </div>
              <div v-else>
                <ul v-for="suggession in suggession_list">
                  <li><strong>{{suggession['suggession:']}} <br/> confidence: {{suggession['confidence:']}}</strong></li>
                </ul>
              </div>
            </div>
          </div>
          
          <div class="col-sm-6 col-md-6 mobile-padding">
            <div class="sentiment-summary">
              <h2>Sentiment Analysis</h2>
              <p class="sentiment-score"></p>
              <!-- <p class="sentiment-text">The sentiments of customer will apear here.</p> -->
              <div v-if="sentiment_processing">
                <i class="fa fa-spinner fa-spin"></i>
              </div>
              <div v-else>
                <ul v-for="sentiment in sentiment_list">
                  <li><strong>{{sentiment['sentiment:']}} <br/> confidence: {{sentiment['sentiment_level:']}}</strong></li>
                </ul>
              </div>
            </div>
          </div>
        </div>
      </div>

<!--container for toxicity analysis and conversation summary -->
<div class="container">
        <div class="row">
          <div class="col-sm-6 col-md-6 padding" >
          <!--row split for summary and suggestions-->
            <div class="sentiment-summary">
              <h2>Toxicity Analysis</h2>
              <!-- <ul>
                <li><strong>{{this.main_suggession}}</strong></li>
              </ul> -->
              <div v-if="toxicity_processing">
                <i class="fa fa-spinner fa-spin"></i>
              </div>
              <div v-else>
                <ul v-for="toxicity in toxicity_list">
                  <li><strong>{{toxicity['toxicity']}} <br/> confidence: {{toxicity['toxicity_level']}}</strong></li>
                </ul>
              </div>
            </div>
          </div>
          
          <div class="col-sm-6 col-md-6 mobile-padding  padding">
            <div class="sentiment-summary">
              <h2>Conversation Summary</h2>
              <p>(for futher follow up)</p>
              <div v-if="summarize_processing">
                <i class="fa fa-spinner fa-spin"></i>
              </div>
              <div v-else>
              <p class="sentiment-text">{{this.summary}}</p>
              </div>
              <!-- <ul v-for="sentiment in sentiment_list">
                <li><strong>{{sentiment['sentiment:']}} <br/> confidence: {{sentiment['sentiment_level:']}}</strong></li>
              </ul> -->
            </div>
          </div>
        </div>
      </div>
</template>



<script>
// speech to text library
import annyang from 'annyang';
// axios is a library for sending http requests in vuejs
import axios from 'axios';

const axiosInstance = axios.create({
  baseURL: 'https://clientsensebackend-production.up.railway.app'
  // 'http://127.0.0.1:8000'
});

export default {
  data() {
    return {
      recognition: null,
      // variable to store the transcribed data
      transcript: '',
      // variable to store conversation summary
      summary: '',
      // a state variable to show the listening status on the page
      listening: false,
      // autocorrect processing status variable
      autocorrect_processing: false,
      // summarize processing status variable
      summarize_processing: false,
      // sentiment processing status variable
      sentiment_processing: false,
      // toxicity processing status variable
      toxicity_processing: false,
      // suggession processing status variable
      suggession_processing: false,
      // primary suggession variable
      main_suggession: '',
      // list of dict for other suggession and their respective confidence level 
      suggession_list: [],
      // list of sentiments of the customer
      sentiment_list: [],
      // main sentiment variable
      main_sentiment: null,
      // list of toxicity
      toxicity_list: []
    };
  },


  mounted() {
   // initializing annyang a js library to convert speech to text 
   annyang.addCallback('result', (transcript) => {
     
      this.transcript += transcript[0];
      if (transcript[0].length >=15){
       console.log("autocorrect called")
       console.log()
       this.autocorrect();
      }
      // Vue.set(this, 'transcript', this.transcript + transcript[0]);
    });
  },
  methods: {
    // function that starts and stops listening on 'Listen' button click,
    // it uses the 'listening' boolean to toggle the states
    startListening() {
      // toggle variable
      this.listening = !this.listening
      console.log(this.listening)
      if (this.listening) {
        // start listening when listen button is toggled on.
        annyang.start({
          autoRestart: true,
          continuous: true
        });
       
      } else {
        // stop listening when listen button is toggled off.
        annyang.abort()
      }
    },
    // used for correcting the grammatical and spelling mistakes in the transcribed text
    //  used the open ai api on the backend
    async autocorrect() {
      console.log("input for auto correcting:", this.transcript)
      this.autocorrect_processing = true
      try {
        const response = await axiosInstance.post('/autocorrect/', {
          text: this.transcript
        },
        {
                    headers: {
                      "Content-Type": "application/x-www-form-urlencoded",
                    },
        });
        // obtaining corrected text response from open ai api on backend 
        // and storing it to the transcript variable to show on text area input field
        this.transcript = response.data["response"]["choices"][0]["text"];
        // this.transcript = this.transcript.replace(/\n\n/g, "");

        // console.log(response.data["response"]["choices"][0]["text"])
      } catch (error) {
        console.error(error);
      }
      this.autocorrect_processing = false
    },


  
  // method to get sales summary
  async summarize() {
      console.log("input for summarizing:", this.transcript)
      this.summarize_processing = true
      try {
        const response = await axiosInstance.post('/summarize/', {
          text: this.transcript
        },
        {
                    headers: {
                      "Content-Type": "application/x-www-form-urlencoded",
                    },
        });
        
        this.summary = response.data.summary
      } catch (error) {
        console.error(error);
      }
      this.summarize_processing = false
    },


// method to get sales conversation suggessions
    async getSuggession() {
      this.getSentiments()
      this.summarize()
      this.getToxicity()
      this.suggession_processing = true
      try {
        const response = await  axiosInstance.post('/suggessions/', {
          text: this.transcript,
        },
        {
                    headers: {
                      "Content-Type": "application/x-www-form-urlencoded",
                    },
        });
        console.log("suggession response", response.data.main_suggession)
        this.main_suggession = response.data.main_suggession;
        this.suggession_list = response.data.suggession_list;
        console.log("suggession list", this.suggession_list)
      } catch (error) {
        console.error(error);
      }
      this.suggession_processing = false

    },


// method to get customer sentiments
    async getSentiments() {
      this.sentiment_processing = true
      console.log("enetered sentiment request")
      try {
        const response = await  axiosInstance.post('/sentiment/', {
          text: this.transcript,
        },
        {
                    headers: {
                      "Content-Type": "application/x-www-form-urlencoded",
                    },
        });
        
        this.sentiment_list = response.data.sentiment_list;
        this.main_sentiment = response.data.highest_sentiment;

        console.log("main sentiment", this.main_sentiment)
      } catch (error) {
        console.error(error);
      }
      this.sentiment_processing = false

    },



// method to get toxicity
    async getToxicity() {
      this.toxicity_processing = true
      console.log("enetered toxicity request")
      try {
        const response = await  axiosInstance.post('/toxicity/', {
          text: this.transcript,
        },
        {
                    headers: {
                      "Content-Type": "application/x-www-form-urlencoded",
                    },
        });
        
        this.toxicity_list = response.data.toxicity_list;
        console.log("toxicity list", this.toxicity_list)
      } catch (error) {
        console.error(error);
      }

      this.toxicity_processing = false
    },


  }
};
</script>

<style scoped>
.gradient-button {
  background: linear-gradient(to right, #00b7ff, #2d5be3);
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 25px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  cursor: pointer;
  transition: all 0.5s ease;
}

.gradient-button:hover {
  background: linear-gradient(to left, #00b7ff, #2d5be3);
  color: white;
  box-shadow: 0px 0px 20px #2d5be3;
}

.autocorrect-button{
  background:#2d5be3;
  size: 10px;
  float: right;
  top: 10px;
}

.getsuggession-button{
  background:#2d5be3;
  size: 10px;
  float: left;
  margin-right:20px;
  top: 10px;
  
}
</style>
