<template>
  <div class="container">
    <div>
      <Logo />
      <h1 class="title">
        marsWeather
      </h1>
      <!-- <NuxtLink to="/rems.json">API REMS (tiempo en Marte ahora)</NuxtLink> -->
      <a href="/rems.json" target="_blank">API REMS (tiempo en Marte ahora)</a>
     
    </div>
  </div>
</template>

<script>
const xml2js = require('xml2js');
const fs = (process.server ? require('fs-extra') : null)


export default {
  data() {
      return {
        rems: [],
        currentweather: []
      }
  },

  async fetch() {

    this.currentweather = await fetch('/static/rems.json')
      .then(res => res.json());   

    const xmlData = await fetch('http://cab.inta-csic.es/rems/rems_weather.xml')
      .then(res => res.text());        

    this.rems = await this.xmlToJSON(xmlData);
    // uncoment to generate 
    //this.saveJSON(this.rems);
  },

  methods: {
    xmlToJSON: (str, options) => {
      return new Promise((resolve, reject) => {
        xml2js.parseString(str, options, (err, jsonObj) => {
          if (err) {
            return reject(err);
          }
          resolve(jsonObj);
        });
      });
    },

    saveJSON: (data) => {
      const file = './static/rems.json'
     
    
      fs.outputJson(file, data)
        .then(() => fs.readJson(file))
        .then(data => {
          console.log("success")
        })
        .catch(err => {
          console.error(err)
        })
      }

    }
 
}
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family:
    'Quicksand',
    'Source Sans Pro',
    -apple-system,
    BlinkMacSystemFont,
    'Segoe UI',
    Roboto,
    'Helvetica Neue',
    Arial,
    sans-serif;
  display: block;
  font-weight: 300;
  font-size: 4rem;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
