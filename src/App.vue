<template>
  <div class="container mt-3" id="app">
    <center>
      <button type="button" @click="getFirst('')" class="button is-light is-primary mr-2">now</button>
      <button type="button" @click="getFirst('1d')" class="button is-light is-primary mr-2">24h</button>
      <button type="button" @click="getFirst('7d')" class="button is-light is-primary mr-1">7d</button>
      <button type="button" @click="getFirst('30d')" class="button is-light is-primary ml-1">30d</button>
      <button type="button" @click="getFirst('year')" class="button is-light is-primary ml-2">year</button>
    </center>
     <center class="mt-3">
            <button type="button" @click="setTrending(1)" class="button is-light is-success ml-2 ">1</button>
            <button type="button" @click="setTrending(2)" class="button is-light is-success ml-2">2</button>
            <button type="button" @click="setTrending(3)" class="button is-light is-success ml-2">3</button>
            <button type="button" @click="setTrending(5)" class="button is-light is-success ml-2">5</button>
            <button type="button" @click="setTrending(10)" class="button is-light is-success ml-2 mr-2">10</button>
            <button type="button" @click="setTrending(15)" class="button is-light is-success mr-2">15</button>
            <button type="button" @click="setTrending(20)" class="button is-light is-success mr-2">20</button>
        </center>
    <center class="mt-2">
      <button type="button" @click="screenCap()" class="button is-success">บันทึกภาพ</button>
    </center>
    <center class="mt-3 cappy table-wrapper has-mobile-cards" v-if="showTable">
      <p>Top Hashtags in Thailand</p>
      <table class="table is1-striped is-bordered">
        <thead>
          <tr>
            <th>#</th>
            <th>Hastag</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in Data" :key="item" :id="'table_'+item.index">
            <td>{{item.index}}</td>
            <td><a :href="'https://twitter.com/search?q='+encodeURIComponent(item.hastag)+'&src=trend_click'">{{item.hastag}}</a><br><span class="tag is-light is-info mr-1">{{item.tweets}}</span><span
                class="tag is-light is-primary">{{item.record}}</span></td>
            <td><button type="button" @click="screenCap2(item.index)" class="button is-success">{{item.index}}</button>
            </td>
          </tr>
        </tbody>
        <!---->
      </table>
    </center>
    <center class="mt-3 cappy table-wrapper has-mobile-cards" v-else>
      <p>Top Hashtags in Thailand</p>
      <table class="table is1-striped is-bordered">
        <thead>
          <tr>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in Data" :key="item" :id="'table_'+item.index">
            <td><center>{{item.hastag}}<br><span class="tag is-light is-info mr-1">{{item.tweets}}</span><span
                class="tag is-light is-primary">{{item.record}}</span></center></td>
          </tr>
        </tbody>
        <!---->
      </table>
    </center>
  </div>
</template>

<script>
  const axios = require('axios');
  const html2canvas = require('html2canvas');
  const baseUrl = 'https://api-vue-sv1.herokuapp.com';
  export default {
    name: 'App',
    components: {},
    data() {
      return {
        Data: [],
        showTable: false,
        loopIndex:20,
        timerec:""
      }
    },
    methods: {
      getFirst: function (timerec) {
        this.timerec = timerec;
        if (this.Data != []) {
          this.showTable = false;
          this.Data = [];
          axios
          .get(baseUrl + '/twitter/trending/thailand/' + timerec)
          .then((response) => {
            for (let index = 0; index < this.loopIndex; index++) {
              this.Data.push(response.data[index]);
            }
            this.showTable = true;
          })
          .catch((error) => {
            console.error(error)
          });
        }else{
          axios
          .get(baseUrl + '/twitter/trending/thailand/' + timerec)
          .then((response) => {
            for (let index = 0; index < this.loopIndex; index++) {
              this.Data.push(response.data[index]);
            }
            this.showTable = true;
          })
          .catch((error) => {
            console.error(error)
          });
        }
      },
      setTrending(index){
        this.loopIndex = index
        this.getFirst(this.timerec);
      },
      screenCap: function () {
        this.showTable = false;
        setTimeout(() => {
          var offScreen = document.querySelector('.table');
          html2canvas(offScreen, {
            scrollY: -window.scrollY
          }).then((canvas) => {
            var dataURL = canvas.toDataURL("image/png", 1.0);
            var link = document.createElement('a');
            link.href = dataURL;
            link.download = 'twitter_trending.png';
            this.imgDataUrl = dataURL;
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
          });
        }, 20);
        setTimeout(() => {
          this.showTable = true;
        }, 500);
      },
      screenCap2: function (id) {
        this.showTable = false;
        setTimeout(() => {
          var offScreen = document.querySelector('#table_' + id);
          html2canvas(offScreen, {
            scrollY: -window.scrollY
          }).then((canvas) => {
            var dataURL = canvas.toDataURL("image/png", 1.0);
            var link = document.createElement('a');
            link.href = dataURL;
            link.download = 'twitter_trending_' + id + '.png';
            this.imgDataUrl = dataURL;
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
          });
        }, 20);
        setTimeout(() => {
          this.showTable = true;
        }, 500);

      },
    },
    mounted() {},
    created() {
      this.getFirst('1d');
    }
  }
</script>