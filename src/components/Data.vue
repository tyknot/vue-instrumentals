<template>
  <!-- FILTER TABS -->
  <div id="filterByArtist">
    <div class="d-flex filter-search-col">
      <div class="btn-group btn-group-toggle" data-toggle="buttons">
        <label class="latest-filter btn-sm btn btn-secondary btn-primary active" @click='addActive'>
          <input type="radio" name="options" id="option1" autocomplete="off" checked>Latest
        </label>
        <label class="top-filter btn btn-sm btn-secondary btn-primary" @click="addActive">
          <input type="radio" name="options" id="option2" autocomplete="off">Top
        </label>
      </div>
      <div class="search-input">
        <input type="text" placeholder="Enter Artist, Song.." v-model="search" @input="inputChange">
        <div class="input-group-text"><i class="fa fa-search"></i></div>
      </div>
    </div>
  <!-- ========== -->

  <!-- BEAT INFO -->
    <!-- latest filter tab is active -->
    <div class="latest-active" v-if="latestIsActive">
      <!-- search input is active -->
      <div class="border" v-if="inputActive">
        <div v-for="beat in filterByArtist">
          <section class="beat-section">
            <h6 id="beat-name">{{beat.title}}</h6>
          </section>
          <section class="beat-section2">
            <audio v-if="controlListCheck" controls controlsList="nodownload" oncontextmenu="return false" :src="beat.stream_link"></audio>
            <audio v-else controls controlsList="nodownload" :src="beat.stream_link"></audio>
            <!-- check amount of clicks -->
            <button id="dwnload-btn" type="Button" v-if="downloadCount < 2" class="btn btn-primary" @click="downloadMp3(beat.mp3_link)">Download</button>
            <button id="dwnload-btn2" type="Button" v-else class="btn btn-primary" @click="survey">Download</button>
          </section>
        </div>
      </div>
      <!-- latest/by date paginated data -->
      <div v-else>
        <paginate class="border beat-data" name="beats" :list="beatInfo" :per="10">
          <div v-for="beat in filterByDate"> 
              <section class="beat-section">
                <h6 id="beat-name">{{beat.title}}</h6>
              </section>
              <section class="beat-section2">
                <audio v-if="controlListCheck" controls controlsList="nodownload" oncontextmenu="return false" :src="beat.stream_link"></audio>
                <audio v-else controls controlsList="nodownload" :src="beat.stream_link"></audio>
                <!-- check amount of clicks -->
                <button id="dwnload-btn" type="Button" v-if="downloadCount < 2" class="btn btn-primary" @click="downloadMp3(beat.mp3_link)">Download</button>
                <button id="dwnload-btn2" type="Button" v-else class="btn btn-primary" @click="survey">Download</button>
              </section>
          </div>
        </paginate>
        <paginate-links id="paginate" for="beats" 
         :show-step-links="true"
         :step-links="{
         next: 'Next',
         prev: 'Prev'}"
         :limit="5">
        </paginate-links>
      </div>
    </div>

    <!-- Top filter tab is active -->
    <div class="top-active" v-else-if='topIsActive'>
      <div v-if="inputActive">
        <div v-for="beat in filterByArtist">
          <section class="beat-section">
            <h6 id="beat-name">{{beat.title}}</h6>
          </section>
          <section class="beat-section2">
            <audio v-if="controlListCheck" controls controlsList="nodownload" oncontextmenu="return false" :src="beat.stream_link"></audio>
            <audio v-else controls controlsList="nodownload" :src="beat.stream_link"></audio>
            <!-- check amount of clicks -->
            <button id="dwnload-btn" type="Button" v-if="downloadCount < 2" class="btn btn-primary" @click="downloadMp3(beat.mp3_link)">Download</button>
            <button id="dwnload-btn2" type="Button" v-else class="btn btn-primary" @click="survey">Download</button>
          </section>
        </div>
      </div>
      <!-- top paginated data -->
      <div v-else>
        <paginate class="border beat-data" name="beats" :list="beatInfo" v-for="beat in randomize" :per="10">
              <section class="beat-section">
                <h6 id="beat-name">{{beat.title}}</h6>
              </section>
              <section class="beat-section2">
                <audio v-if="controlListCheck" controls controlsList="nodownload" oncontextmenu="return false" :src="beat.stream_link"></audio>
                <audio v-else controls controlsList="nodownload" :src="beat.stream_link"></audio>
                <!-- check amount of clicks -->
                <button id="dwnload-btn" type="Button" v-if="downloadCount < 2" class="btn btn-primary" @click="downloadMp3(beat.mp3_link)">Download</button>
                <button id="dwnload-btn2" type="Button" v-else class="btn btn-primary" @click="survey">Download</button>
              </section>
        </paginate>
        <!-- page selection -->
        <paginate-links id="paginate" for="beats" 
         :show-step-links="true"
         :step-links="{
         next: 'Next',
         prev: 'Prev'}"
         :limit="5">
        </paginate-links>
      </div>
    </div>
  </div>
  <!-- ========== -->
</template>
<script>
import wavePlayer from '@/components/Wave.vue';

const url  = 'http://127.0.0.1:8000/api/v1?format=json'

export default{
  name: 'beat-data',
  props: {
    title: '',
  },
  components:{
    wavePlayer,
  },
  data(){
    return {
      beatInfo: [],
      paginate: ['beats'],
      downloadBtnStatus: false,
      downloadCount: 0,
      controlListCheck: true,
      testmenuCheck: true,
      topIsActive: false,
      latestIsActive: true,
      inputActive: false,
      search: '',
    }
  },
  mounted(){
    axios.get(url)
    .then(response => this.beatInfo = response.data)
    // local not present
    const storeDownloadCount = () => {
      if(localStorage.getItem('downloadCount') == 0){
        localStorage.setItem('downloadCount', this.downloadCount)
        console.log('local not activee')
      // local present
      }else if(localStorage.getItem('downloadCount') > 0){
        this.downloadCount = localStorage.getItem('downloadCount')
      }
    }
    storeDownloadCount()
  },
  // check if somebody edited audio player html attr
  watch: {
    downloadCount:{
      handler(){ 
        // set item
        localStorage.setItem('downloadCount', this.downloadCount)
      }
    }
  },
  computed: {
    filterByArtist: function(){
      return this.beatInfo.filter((beat) =>{
        const title = beat.title.toUpperCase()
        return title.match(this.search.toUpperCase())
      })
      .sort((a,b)=>{
        return new Date(b.date) - new Date(a.date)
      })
    },
    filterByDate: function(){
      // seems to filter the newest of each page
      // not filtering all
      return this.paginated('beats')
      .sort((a,b)=>{
        return new Date(b.date) - new Date(a.date)
      })
    },
    randomize: function(){
      return this.paginated('beats')
      .sort(beat=>{
        return Math.random() - this.paginated('beats').length
      })
    }
  },
  methods: {
    // test things
    tryMe:function(){
      this.per = this.beatInfo
      console.log(this.per)
    },
    // search input
    inputChange: function(){
      if(this.search.length > 0){
        return this.inputActive = true
      }else{
        return this.inputActive = false
      }
    },
    // FILTER LATEST/TOP
    addActive: function(el){
      const target = el.target
      const latestFilter = document.querySelector('.latest-filter')
      const topFilter = document.querySelector('.top-filter')

      if(target.classList.contains('latest-filter')){
        target.classList.add('active')
        topFilter.classList.remove('active')
        if(target.classList.contains('active')){
          this.latestIsActive = true
          this.topIsActive = false
        }
      }
      if(target.classList.contains('top-filter')){
        target.classList.add('active')
        latestFilter.classList.remove('active')
        if(target.classList.contains('active')){
          this.latestIsActive = false
          this.topIsActive = true
          
        } 
      }
    },
    // cpa locker goes here
    survey: function(){
      alert('need to finish survey!!!!')
      localStorage.removeItem('downloadCount')
      this.downloadCount = 0
    },
    // download beat mp3
    downloadMp3: function(url){
      this.downloadCount++
      window.location.href = url
    },
  }
};
</script>
<style>
.beat-data{
  padding: 0;
}
.beat-section{
  display: -webkit-box; 
  display: -moz-box;
  display: -ms-flexbox;
  display: -webkit-flex; 
  display: flex;
  align-items: center;
  border: 4px solid gainsboro;
  padding: 19px;
  margin: 10px;
  background: gainsboro;
}
.beat-section > h6{
  margin: auto;
}
.beat-section2{
  display: -webkit-box; 
  display: -moz-box;
  display: -ms-flexbox;
  display: -webkit-flex; 
  display: flex;
  align-items: center;
  justify-content: space-around;
}
.border{
  border: 2px solid gainsboro;
  margin: 5px;
}
div[name="beatInfo"]{
  width: 95%;
  display: flex;
  flex-direction: column;
  margin: auto;
}
.btn-group{
  left: 5px;
}
.search-input{
  margin-left: auto;
  display: flex;
  position: relative;
  left: 40px;
}
.search-input > div{
  position: relative !important;
  right: 47px !important;
  border: none !important;
  background: transparent;
}
.search-input > label{
  border: 1px solid gray;
  padding: 2px 10px;
  background: #94b8b8;
  color: white;
}
.search-input > input{
  width: 182px;
  text-align: left;
  border-radius: 5px;
  border: 1px solid slategray;
  padding-left: 18px;
}
/* == PAGINATION == */
#paginate{
  display: -webkit-box; 
  display: -moz-box;
  display: -ms-flexbox;
  display: -webkit-flex; 
  display: flex;
  justify-content: flex-end;
  margin-top: 10px;
  position: relative;
  right: 5px;
  cursor: pointer;
  user-select: none;
  -moz-user-select: none;
  -webkit-user-select: none;
}
/* Current page */
ul.paginate-links > li.active > a{
  border: 1px solid;
  padding: 3px;
  color: white;
  background: #1e2f2f !important;
}
ul.paginate-links > li.active > a:hover{
  color: white;
}
/* Disabled Next/Previous Links */
ul.paginate-links > li.disabled > a{
  background: #94b8b8;
  opacity: 0.8;
}
/* Previous */
ul.paginate-links > li.left-arrow > a{
  background: white;
  border: 1px solid gainsboro;
  padding: 5px;
}
/* Next */
ul.paginate-links > li.right-arrow > a{
  background: white;
  border: 1px solid gainsboro;
  padding: 5px;
}
/* Numbers */
ul.paginate-links > li.number > a{
  border: 1px solid gainsboro;
  padding: 5px;
  background: white;
}
.ellipses{
  background: white;
  border: 1px solid gainsboro;
  padding: 3px;
  position: relative;
  bottom: 5px;
  height: 30px;
}

@media (max-device-width:500px){
    .search-input{
      margin: auto;
    }
    .filter-search-col{
      flex-direction: column;
    }
    .btn-group{
      justify-content: center;
      left: none;
      margin: auto;
      padding: 13px;
    }
    .beat-section2{
      flex-direction: column;
      padding: 10px;
    }
}
</style>
