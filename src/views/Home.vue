<template>
  <div class="home">
    <!-- <img alt="Vue logo" src="../assets/logo.png" /> -->
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    <header>
      <div class="logo"></div>
      <div class="bar-container" :class="{ active: toggle == 1 }">
        <div class="bar-wrapper">
          <div class="ctl-container">
            <div class="dropdown" @click="toggle = !toggle">
              {{ selectedCity.CityName }}
              /
              {{ selectedType.Zh }}
            </div>
            <input
              class="search"
              name="taiwantravel-search"
              placeholder="請輸入關鍵詞"
              v-model="queryString"
              @keypress.enter="enterClicked()"
            />
            <div class="submit" @click="enterClicked()">SEARCH</div>
          </div>
        </div>

        <div class="dropdown-container" :class="{ active: toggle == 1 }">
          <h2>選擇區域</h2>
          <button
            @click="selectedCity = defaultCity"
            :class="{ active: selectedCity == defaultCity }"
          >
            台灣
          </button>
          <div class="tabs-container">
            <div class="radio-container">
              <input
                type="radio"
                class="tabs-radio"
                name="tabs"
                v-for="(city, index) in Object.keys(cities)"
                :key="city.id"
                :id="'home-tab-' + index"
                :checked="index == 0"
              />

              <label
                v-for="(city, index) in Object.keys(cities)"
                :key="city.id"
                :for="'home-tab-' + index"
                class="tabs-label"
                :id="'label-' + index"
                >{{ cities[city].Zh }}</label
              >
              <div class="cities-container">
                <div class="cities-slide">
                  <div class="cities-wrapper" v-for="area in Object.keys(cities)" :key="area.id">
                    <button
                      v-for="cts in cities[area].Cities"
                      :key="cts.City"
                      @click="selectedCity = cts"
                      :class="{ active: selectedCity == cts }"
                    >
                      {{ cts.CityName }}
                    </button>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <h2>選擇類型</h2>
          <div class="type-container">
            <button
              v-for="type in types"
              :key="type.En"
              @click="selectedType = type"
              :class="{ active: selectedType == type }"
            >
              {{ type.Zh }}
            </button>
          </div>

          <div class="completion-container">
            <button class="active" @click="toggle = !toggle">OK</button>
          </div>
        </div>
      </div>
    </header>
    <div class="cat-container">
      <h2>熱門景點</h2>
      <div class="scenicspots-container">
        <div
          v-for="(scenicspot, index) in scenicspots"
          :key="scenicspot.ScenicSpotID"
          class="scenicspots-item"
          :class="{ l: index % 3 == 0 }"
        >
          <router-link
            :to="{
              path: '/information/' + 'scenicspot' + '/Taiwan/' + scenicspot.ScenicSpotID,
            }"
          >
            <div
              class="bgi"
              :style="{
                'background-image': 'url(' + scenicspot.Picture.PictureUrl1 + ')',
              }"
            ></div>
            <span> {{ scenicspot.ScenicSpotName }}</span>
          </router-link>
        </div>
      </div>
      <h2>美食推薦</h2>
      <div class="restaurants-container">
        <div
          v-for="restaurant in restaurants"
          :key="restaurant.RestaurantID"
          class="restaurants-item"
        >
          <router-link
            :to="{
              path: '/information/' + 'restaurant' + '/Taiwan/' + restaurant.RestaurantID,
            }"
          >
            <div
              class="bgi"
              :style="{
                'background-image': 'url(' + restaurant.Picture.PictureUrl1 + ')',
              }"
            ></div>
            <h2 class="title">{{ restaurant.Name }}</h2>
            <p class="time"><span class="time-icon"></span>{{ restaurant.OpenTime }}</p>
            <p class="address"><span class="address-icon"></span>{{ restaurant.Address }}</p>
          </router-link>
        </div>
      </div>

      <h2>旅宿資訊</h2>
      <div class="hotels-container">
        <div v-for="hotel in hotels" :key="hotel.HotelID" class="hotels-item">
          <router-link
            :to="{
              path: '/information/' + 'hotel' + '/Taiwan/' + hotel.HotelID,
            }"
          >
            <div
              class="bgi"
              :style="{
                'background-image': 'url(' + hotel.Picture.PictureUrl1 + ')',
              }"
            ></div>
            <h2 class="title">{{ hotel.Name }}</h2>
            <p class="address"><span class="address-icon"></span>{{ hotel.Address }}</p>
          </router-link>
        </div>
      </div>
      <h2>節慶活動</h2>
      <div class="activities-container">
        <div v-for="activity in activities" :key="activity.ActivityID" class="activity-item">
          <router-link
            :to="{
              path: '/information/' + 'activity' + '/Taiwan/' + activity.ActivityID,
            }"
          >
            <div class="img">
              <div
                class="bgi"
                :style="{
                  'background-image': 'url(' + activity.Picture.PictureUrl1 + ')',
                }"
              ></div>
            </div>
            <div class="content">
              <h2 class="title">{{ activity.Name }}</h2>
              <p class="address"><span class="address-icon"></span>{{ activity.Address }}</p>
              <br />
              <p>{{ activity.Description }}</p>
            </div>
          </router-link>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import _ from 'lodash';
import JsSHA from 'jssha';

import cities from '../components/Cities';
import types from '../components/Types';

function getAuthorizationHeader() {
  const AppID = '705e9a212c3242ed9a2fa2355b84f418';
  const AppKey = 'o2tSBueG3Dtk4o--mJKUv5kmGlE';

  const GMTString = new Date().toUTCString();
  const shaObj = new JsSHA('SHA-1', 'TEXT');
  shaObj.setHMACKey(AppKey, 'TEXT');
  shaObj.update(`x-date: ${GMTString}`);
  const HMAC = shaObj.getHMAC('B64');
  const Authorization = `hmac username="${AppID}", algorithm="hmac-sha1", headers="x-date", signature="${HMAC}"`;
  return { Authorization, 'X-Date': GMTString };
}

function getRestaurants5() {
  return new Promise((resolve) => {
    const api = 'https://ptx.transportdata.tw/MOTC/v2/Tourism/Restaurant?$filter=Picture%2FPictureUrl1%20ne%20null&$format=JSON';
    return axios.get(api, { headers: getAuthorizationHeader() }).then((response) => {
      const newData = _.shuffle(response.data);
      resolve(newData.slice(0, 5));
    });
  });
}

function getScenicSpot7() {
  return new Promise((resolve) => {
    const api = 'https://ptx.transportdata.tw/MOTC/v2/Tourism/ScenicSpot?'
      + '$filter=Picture%2FPictureUrl1%20ne%20null&'
      + '$format=JSON';
    return axios.get(api, { headers: getAuthorizationHeader() }).then((response) => {
      const newData = _.shuffle(response.data);
      resolve(newData.slice(0, 7));
    });
  });
}

function getHotel6() {
  return new Promise((resolve) => {
    const api = 'https://ptx.transportdata.tw/MOTC/v2/Tourism/Hotel?'
      + '$filter=Picture%2FPictureUrl1%20ne%20null&'
      + '$format=JSON';
    return axios.get(api, { headers: getAuthorizationHeader() }).then((response) => {
      const newData = _.shuffle(response.data);
      resolve(newData.slice(0, 7));
    });
  });
}
function shortDescription(description) {
  let temp;
  const num = 100;
  if (description.length > num) {
    temp = description.slice(0, num);
    temp += '......';
    return temp;
  }
  return description;
}
function getActivities6() {
  return new Promise((resolve) => {
    const api = 'https://ptx.transportdata.tw/MOTC/v2/Tourism/Activity?'
      + '$filter=Picture%2FPictureUrl1%20ne%20null&'
      + '$format=JSON';
    return axios.get(api, { headers: getAuthorizationHeader() }).then((response) => {
      let newData = _.shuffle(response.data);
      newData = newData.map((element) => {
        const temp = element;
        temp.Description = shortDescription(element.Description);
        return temp;
      });
      resolve(newData.slice(0, 7));
    });
  });
}
const defaultCity = { CityName: '台灣', City: 'Taiwan' };
export default {
  name: 'Home',
  components: {
    // HelloWorld,
  },
  data() {
    return {
      title: 'test',
      scenicspots: [],
      restaurants: [],
      hotels: [],
      activities: [],
      cities,
      defaultCity,
      selectedCity: defaultCity,
      types,
      selectedType: types[0],
      toggle: 0,
      queryString: '',
    };
  },
  methods: {
    enterClicked() {
      this.$router.push(`/${this.selectedType.En}/${this.selectedCity.City}/${this.queryString}`);
    },
  },
  computed: {},
  async mounted() {
    this.scenicspots = await getScenicSpot7();
    this.restaurants = await getRestaurants5();
    this.hotels = await getHotel6();
    this.activities = await getActivities6();
    console.log(Object.keys(cities));
    console.log(this.defaultCity);
    console.log(this.selectedCity);
  },
};
</script>

<style lang="scss" scoped>
$blue: #a6cde0;
a {
  height: 100%;
  width: 100%;
  z-index: 10;
}
@mixin shadow {
  box-shadow: 5px 5px 5px rgba(0, 0, 0, 0.2);
}
header {
  width: 100%;
  height: 100vh;
  background-image: url('../assets/images/header.jpg');
  background-position: center;
  background-size: cover;
  display: flex;
  align-items: center;
  flex-direction: column;
  .logo {
    margin-top: 95px;
    max-width: 500px;
    width: 100%;
    max-height: 500px;
    height: 100%;

    background-image: url('../assets/images/taiwan-logo-white.svg');
    background-position: center;
    background-repeat: no-repeat;
    background-size: contain;
    @media (max-width: 768px) {
      margin-top: 70px;
    }
  }
  .bar-container {
    overflow: hidden;
    position: absolute;
    top: 540px;
    width: calc(100% - 80px);
    max-width: 600px;
    border-radius: 20px;
    display: inline-flex;
    justify-content: space-evenly;
    align-items: flex-start;
    $items-height: 40px;
    padding: 20px;
    padding-top: 0;
    @media (max-width: 768px) {
      display: none;
    }
    &.active {
      padding-bottom: 331px;
      @media (max-width: 768px) {
        padding-bottom: 310px;
      }
    }

    @media (max-width: 768px) {
      top: 430px;
    }
    .bar-wrapper {
      .ctl-container {
        width: 100%;
        height: 100%;
        display: flex;
        gap: 10px;
        z-index: 20;
      }
      @include shadow();
      padding: 20px 20px;
      border-radius: 20px;
      background-color: #fff;
      width: 100%;
      display: flex;
      gap: 10px;
      position: relative;
      input {
        &::placeholder {
          color: #b0b0b0;
        }
      }
      .dropdown,
      .search {
        width: 100%;
        flex: 3 1 200px;
        height: $items-height;

        line-height: $items-height;
        border-radius: 10px;
        background-color: #ebebeb;
        color: #707070;
        padding-left: 10px;
        font-size: 18px;
        z-index: 3;
        @media (max-width: 768px) {
        }
      }
      .dropdown {
        cursor: pointer;
        position: relative;
        display: flex;
        align-items: center;
      }
      .search {
        border: 0px;
        &:focus {
          outline: none;
        }
      }
      .submit {
        flex: 1 1 70px;
        height: $items-height;
        line-height: $items-height;
        border-radius: 10px;
        background-color: #e8cb89;
        color: #fff;
        text-align: center;
        cursor: pointer;
        padding: 0 10px;
        z-index: 3;
        @media (max-width: 768px) {
          width: 100%;
          flex: 3 1 200px;
          padding-left: 10px;
          padding-right: 0;
        }
      }
      @media (max-width: 768px) {
        bottom: 0px;
        height: 120px;
        display: flex;
        flex-direction: column;
        // padding: 20px;
        padding-bottom: 40px;
      }
      &::before {
        background-color: #fff;

        width: 100%;
        height: calc(100% - 20px);
        border-radius: 20px;
        content: '';
        position: absolute;
        top: 0;
        left: 0;
        z-index: 19;
      }
    }

    .dropdown-container {
      &.active {
        display: block;
        top: 70px;
        width: calc(100% - 40px - 40px);
      }
      @include shadow();
      box-shadow: 5px 5px 5px 5px rgba(0, 0, 0, 0.2);
      padding: 0px 20px;
      padding-bottom: 20px;
      position: absolute;
      top: -350px;
      transition: all 0.5s;

      width: calc(100% - 40px - 40px - 150px);
      background-color: #fff;
      z-index: 3;
      border-radius: 20px;

      h2 {
        display: inline-block;
        margin-right: 10px;
      }
      button {
        width: 62px;
        display: inline-flex;
        justify-content: center;
        align-items: center;
        background-color: #fff;
        border: solid 1px $blue;
        padding: 10px;
        border-radius: 10px;
        height: 40px;
        cursor: pointer;
        &:hover,
        &.active {
          background-color: $blue;
          color: #fff;
        }
      }
      .tabs-container {
        width: 100%;
        .radio-container {
          display: block;
          width: 100%;

          .cities-container {
            width: 100%;
            height: 40px;
            margin-top: 20px;
            position: relative;
            overflow: hidden;
            @media (max-width: 768px) {
              height: 90px;
            }
            .cities-slide {
              display: inline-block;
              white-space: nowrap;

              position: absolute;
              top: 0px;
              left: 0px;
              transition: all 0.5s;
              .cities-wrapper {
                width: 100%;
                height: 100%;
                display: inline-flex;
                gap: 10px;
                flex-wrap: wrap;
                p {
                  display: inline;
                }
              }
            }
          }
          label {
            padding-left: 2px;
            padding-bottom: 2px;
            padding-right: 8px;
            margin-right: 8px;
            cursor: pointer;
          }
          [type='radio'] {
            display: none;
          }

          @for $i from 1 through 10 {
            input:nth-of-type(#{$i}):checked ~ label:nth-of-type(#{$i}) {
              border-bottom: 3px solid $blue;
              z-index: 2;
            }
            input:nth-of-type(#{$i}):checked ~ .cities-container .cities-slide {
              transform: translateX(($i - 1) * -100%);
            }
          }
        }
      }
      .type-container {
        display: flex;
        gap: 10px;
        margin-bottom: 20px;
      }
      .completion-container {
        border-top: solid 1px #e0e0e0;
        padding-top: 20px;
        display: flex;
        justify-content: flex-end;
      }
    }
  }
}

.cat-container {
  margin: 40px;

  .bgi {
    background-size: cover;
    background-position: center;
    transition: all 0.5s;
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    border-radius: 20px;
    z-index: -1;
  }

  .time-icon,
  .address-icon {
    display: inline-flex;
    flex-shrink: 0;
    height: 13px;
    width: 13px;
    padding-top: 3px;
    background-position: center;
    background-size: contain;
    background-repeat: no-repeat;
    background-image: url('../assets/images/icons/time.svg');
    margin-right: 10px;
  }
  .address-icon {
    background-image: url('../assets/images/icons/site.svg');
  }
  .scenicspots-container {
    $height: 290px;
    display: flex;
    flex-direction: column;
    height: $height;
    flex-wrap: wrap;
    justify-content: space-between;
    gap: 10px;
    @media (max-width: 768px) {
      flex-direction: row;
      height: 100vh;
    }
    .scenicspots-item {
      @include shadow;
      flex-basis: calc($height / 2 - 10px);
      width: 1fr;
      border-radius: 20px;

      position: relative;
      overflow: hidden;
      a {
        display: flex;
        justify-content: center;
        align-items: center;
      }
      &:hover .bgi {
        transform: scale(1.2);
      }
      .bgi::before {
        position: absolute;
        left: 0;
        top: 0;
        content: '';
        display: block;
        width: 100%;
        height: 100%;
        border-radius: 20px;
        z-index: 1;
        background: linear-gradient(rgba(255, 255, 255, 0), #000);
        opacity: 0.7;
        backdrop-filter: grayscale(10%);
        backdrop-filter: contrast(50%);
      }
      span {
        z-index: 2;
        font-size: 16px;
        font-weight: bold;
        color: #f8f8f8;
      }
      @media (max-width: 768px) {
        flex-basis: calc(50% - 7px);
      }
      &.l {
        flex-basis: $height;
        @media (max-width: 768px) {
          flex-basis: 100%;
        }
      }
    }
  }
  .restaurants-container {
    display: flex;
    flex-wrap: wrap;
    gap: 15px;
    .restaurants-item {
      cursor: pointer;
      @include shadow();
      border-radius: 20px;
      height: 450px;

      flex: 1 1 230px;
      display: flex;
      flex-direction: column;
      justify-content: flex-end;
      padding: 10px;
      position: relative;
      overflow: hidden;

      &:hover .bgi {
        overflow: hidden;
        transform: scale(1.2);
      }
      .bgi::before {
        position: absolute;
        left: 0;
        top: 0;
        content: '';
        display: block;
        width: 100%;
        height: 100%;
        border-radius: 20px;
        z-index: 1;

        background: linear-gradient(rgba(255, 255, 255, 0), rgba(0, 0, 0, 0.8));
        opacity: 0.7;
        backdrop-filter: grayscale(10%);
        backdrop-filter: contrast(50%);

        opacity: 1;
      }
      h2,
      p {
        display: flex;
        align-items: flex-start;
        color: #f8f8f8f8;
        z-index: 2;
        padding: 0 10px;
      }
      p {
        margin: 2px 0;
      }
    }
  }
  .hotels-container {
    display: flex;
    flex-wrap: wrap;
    gap: 15px;

    .hotels-item {
      @include shadow();
      border-radius: 20px;
      flex: 1 1 400px;
      height: 300px;
      padding: 10px;
      position: relative;
      display: flex;
      flex-direction: column;

      overflow: hidden;

      .bgi::before {
        position: absolute;
        left: 0;
        top: 0;
        content: '';
        display: block;
        width: 100%;
        height: 100%;
        border-radius: 20px;
        z-index: 1;

        background: linear-gradient(#000, rgba(255, 255, 255, 0));
        opacity: 0.7;
        backdrop-filter: grayscale(10%);
        backdrop-filter: contrast(50%);
      }
      &:hover .bgi {
        transform: scale(1.2);
      }
      h2,
      p {
        color: #f8f8f8;
        z-index: 2;
        display: flex;
        align-items: flex-start;
      }
      p {
        margin: 2px 0;
      }
    }
  }
  .activities-container {
    display: flex;
    flex-direction: column;
    gap: 15px;
    .activity-item {
      @include shadow();
      height: 250px;
      width: 100%;
      border-radius: 20px;
      @media (max-width: 768px) {
        height: auto;
      }

      a {
        border-radius: 20px;
        display: flex;
        position: relative;
        overflow: hidden;
        @media (max-width: 768px) {
          flex-direction: column;
        }
      }

      .img {
        flex: 1 0 250px;
        width: 100%;
        height: 100%;
        border-radius: 20px 0px 0px 20px;
        position: relative;
        overflow: hidden;
        position: relative;
        @media (max-width: 768px) {
          border-radius: 20px 20px 0px 0px;
        }
        .bgi {
          border-radius: 20px 0px 0px 20px;
          @media (max-width: 768px) {
            border-radius: 20px 20px 0px 0px;
          }
        }
        &::before {
          position: absolute;
          left: 0;
          top: 0;
          content: '';
          display: block;
          width: 100%;
          height: 100%;
          border-radius: 20px 0 0 20px;
          z-index: 1;

          opacity: 0.7;
          backdrop-filter: grayscale(10%);
          backdrop-filter: contrast(50%);
        }
      }
      .content {
        padding: 0 20px;
        flex: 2 1 500px;
        p {
          margin: 2px 0;
        }
        z-index: 2;
        @media (max-width: 768px) {
          flex: 2 1 auto;
          padding-bottom: 20px;
        }
      }
      &:hover .img .bgi {
        transform: scale(1.2);
      }
    }
  }
}
</style>
