<template>
  <div class="wrap">
    <div class="header">
      <div class="row align-items-center">
        <div class="col-4">
          <h1 class="mb-2">空氣品質指標 (AQI)</h1>
          <select
            name=""
            id="city"
            class="border-3 border-dark"
            v-model="chosen"
          >
            <option value="預設">請選擇城市</option>
            <option :value="town" v-for="town in city" :key="town">
              {{ town }}
            </option>
          </select>
        </div>
        <div class="col-8">
          <table class="table-bordered border border-dark w-100">
            <tr>
              <td scope="col" class="border-3 bg-safe py-3">0～50</td>
              <td scope="col" class="border-3 bg-regular py-3">51～100</td>
              <td scope="col" class="border-3 bg-nogood py-3">101～150</td>
              <td scope="col" class="border-3 bg-warning py-3">151～200</td>
              <td scope="col" class="border-3 bg-bad py-3">201～300</td>
              <td scope="col" class="border-3 bg-danger py-3">301～400</td>
            </tr>
            <tr>
              <td class="border-3">良好</td>
              <td class="border-3">普通</td>
              <td class="border-3">對敏感族群 不健康</td>
              <td class="border-3">對所有族群 不健康</td>
              <td class="border-3">非常不健康</td>
              <td class="border-3">危害</td>
            </tr>
          </table>
        </div>
      </div>
    </div>
    <div class="branchLine" v-if="chosen !== '預設'">
      <div class="d-flex align-items-center justify-content-between">
        <div class="">
          <h2 class="fz-l">{{ chosen }}</h2>
        </div>
        <div class="dot"></div>
        <div class="d-flex align-items-center justify-content-between">
          <span class="fw-bold date">{{ date }}</span>
          <span class="fw-bold">更新</span>
        </div>
      </div>
    </div>
    <main>
      <div class="row"  v-if="clickedNow">
        <div class="col-4">
          <div class="card border border-3 border-dark">
            <div
              class="
                cardHeader
                row
                align-items-center
                border-bottom border-3 border-dark
                g-0
              "
            >
              <h3 class="col-6 fz-l">{{clickedNow.SiteName}}</h3>
              <h3 class="col-6 fz-xl border-start border-3 border-dark">156</h3>
            </div>
            <div class="cardBody">
              <ul class="p-0 m-0">
                <li
                  class="
                    d-flex
                    pb-3
                    border-bottom border-dark border-2
                    align-items-center
                  "
                >
                  <h4 class="me-2">臭氧</h4>
                  <span>O3 (ppb)</span>
                  <h4 class="ms-auto">21</h4>
                </li>
                <li
                  class="
                    d-flex
                    py-3
                    border-bottom border-dark border-2
                    align-items-center
                  "
                >
                  <h4 class="me-2">懸浮微粒</h4>
                  <span>PM10 (μg/m³)</span>
                  <h4 class="ms-auto">21</h4>
                </li>
                <li
                  class="
                    d-flex
                    py-3
                    border-bottom border-dark border-2
                    align-items-center
                  "
                >
                  <h4 class="me-2">細懸浮微粒</h4>
                  <span>PM2.5 (μg/m³)</span>
                  <h4 class="ms-auto">21</h4>
                </li>
                <li
                  class="
                    d-flex
                    py-3
                    border-bottom border-dark border-2
                    align-items-center
                  "
                >
                  <h4 class="me-2">一氧化碳</h4>
                  <span>CO (ppm)</span>
                  <h4 class="ms-auto">21</h4>
                </li>
                <li
                  class="
                    d-flex
                    py-3
                    border-bottom border-dark border-2
                    align-items-center
                  "
                >
                  <h4 class="me-2">二氧化硫</h4>
                  <span>SO2 (ppb)</span>
                  <h4 class="ms-auto">21</h4>
                </li>
                <li class="d-flex pt-3 align-items-center">
                  <h4 class="me-2">二氧化氮</h4>
                  <span>NO2 (ppb)</span>
                  <h4 class="ms-auto">21</h4>
                </li>
              </ul>
            </div>
          </div>
        </div>
        <div class="col-8">
          <div class="d-flex flex-wrap justify-content-between">
            <div
              class="
                townCard
                cardHeader
                border
                d-flex
                align-items-center
                border-bottom border-3 border-dark
              "
              v-for="item in filterData"
              :key="item.id"
            >
              <a href="#" class="d-block w-50"
                ><h3 class="fz-l">{{ item.SiteName }}</h3></a
              >
              <h3 class="fz-xl border-start border-3 border-dark w-50">
                {{ item.AQI }}
              </h3>
            </div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
// @ is an alias to /src

export default {
  name: "Home",
  components: {},
  data() {
    return {
      data: [],
      city: [],
      towns:[],
      chosen: "預設",
      date: "2019-01-24  14:00",
      clickedNow:""
    };
  },
  methods: {
    getApi() {
      let api =
        "https://data.epa.gov.tw/api/v1/aqx_p_432?format=json&limit=84&api_key=9be7b239-557b-4c10-9775-78cadfc555e9";
      this.$http.get(api).then((response) => {
        this.data = response.data.records;
        console.log(this.data);
        let city = new Set();
        this.data.forEach((item) => {
          city.add(item.County);
        });
        this.city = Array.from(city);
        console.log(this.city);
      });
    },
      getSingleTown(name){
     this.clicked = this.towns.filter(town => {
       return town.SiteName == name
     })
    }

  },
  computed: {
    filterData() {
      if (this.chosen !== "預設") {
      this.towns = this.data.filter((item) => {
          return item.County == this.chosen;
        });
      }
      this.clickedNow = this.towns[0]
      return this.towns;
    },
 
  


  },
  created() {
    this.getApi();
  },
};
</script>

<style lang="scss">
.bg-safe {
  background-color: #95f084;
}

.bg-regular {
  background-color: #ffe695;
}

.bg-nogood {
  background-color: #ffaf6a;
}

.bg-warning {
  background-color: #ff5757;
}

.bg-bad {
  background-color: #9777ff;
}

.bg-danger {
  background-color: #ad1774;
}

.fz-l {
  font-size: 36px;
}
.fz-xl {
  font-size: 48px;
}

.dot {
  border-top: 3px dotted #000;
  width: 786px;
  margin-left: 10px;
  margin-right: 20px;
}

.wrap {
  padding: 40px 85px;
  background: #f0f0f0;
  .header {
    margin-bottom: 32px;
    select {
      width: 350px;
      padding: 16px 20px;
    }
    table {
      td {
        width: 16.666%;
        text-align: center;
        font-weight: bold;
        padding: 0 10px;
      }
    }
  }
  .branchLine {
    .date {
      margin-right: 12px;
      font-size: 14px;
    }
  }
  main {
    h3,
    h4 {
      margin: 0;
    }
    margin-top: 32px;
    .cardHeader {
      h3 {
        padding: 16px 40px;
        text-align: center;
      }
    }
    .cardBody {
      padding: 30px;
      li {
        list-style: none;
      }
      h4 {
        line-height: 1.5;
      }
    }
    .townCard {
      width: 48%;
      margin-bottom: 32px;
    }
  }
}
</style>
