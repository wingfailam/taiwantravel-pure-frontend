<template>
  <div class="information-container">
    <header></header>
    <div class="information-wrapper" v-if="detail">
      <h2 class="title">{{ detail.Name }}</h2>
      <div class="main">
        <div class="informations">
          <h3 class="title">景點資訊</h3>
          <div class="content">
            <div class="date" v-if="detail.StartTime || detail.EndTime">
              <span class="icon icon-calander"></span>
              <span>
                {{ detail.StartTime | timeString('YYYY-MM-DD') }} ~
                {{ detail.EndTime | timeString('YYYY-MM-DD') }}
              </span>
            </div>
            <div class="address" v-if="detail.Address">
              <span class="icon icon-address"></span>
              <a :href="'https://www.google.com/maps/search/' + detail.Name"
                >{{ detail.Address }}
                <span class="icon icon-right icon-link"></span>
              </a>
            </div>
            <div class="phone" v-if="detail.Phone">
              <span class="icon icon-phone"></span>
              <a :href="'tel:' + detail.Phone"
                >{{ detail.Phone }} <span class="icon icon-right icon-link"></span>
              </a>
            </div>
            <div class="openTime" v-if="detail.OpenTime">
              <span class="icon icon-open-time"></span>{{ detail.OpenTime }}
            </div>
            <div class="transportation" v-if="detail.TravelInfo">
              <span class="icon icon-transportation"></span>{{ detail.TravelInfo }}
            </div>
            <div class="official-website" v-if="detail.WebsiteUrl">
              <font-awesome-icon :icon="['fas', 'globe-americas']" />

              <a :href="'tel:' + detail.WebsiteUrl"
                >官方網站 <span class="icon icon-right icon-link"></span>
              </a>
            </div>
            <div class="serviceInfo" v-if="detail.ServiceInfo">
              <span class="icon icon-information"></span>
              ☑ {{ computedServiceInfo(detail.ServiceInfo) }}
            </div>
            <div class="ParkingInfo" v-if="detail.ParkingInfo">
              <span class="icon icon-transportation"></span>
              {{ detail.ParkingInfo.replace('車位:', '可容納：') }}
            </div>
            <div class="organizer" v-if="detail.Organizer">
              <font-awesome-icon :icon="['fas', 'users']" />{{ detail.Organizer }}
            </div>
            <div class="cycle" v-if="detail.Cycle">
              <span class="icon icon-official-website"></span>
              {{ detail.Cycle }}
            </div>
            <div class="tags" v-if="detail.Class1 || detail.Class2 || detail.Class3">
              <font-awesome-icon :icon="['fas', 'tags']" />
              <router-link
                :to="{
                  path: '/' + $route.params.category + '/Taiwan/_' + detail.Class1,
                }"
              >
                <button v-if="detail.Class1">{{ detail.Class1 }}</button>
              </router-link>
              <router-link
                :to="{
                  path: '/' + $route.params.category + '/Taiwan/_' + detail.Class2,
                }"
              >
                <button v-if="detail.Class2">{{ detail.Class2 }}</button>
              </router-link>
              <router-link
                :to="{
                  path: '/' + $route.params.category + '/Taiwan/_' + detail.Class3,
                }"
                ><button v-if="detail.Class3">{{ detail.Class3 }}</button>
              </router-link>
            </div>
          </div>
        </div>
        <div class="images">
          <div class="my-swiper-container">
            <swiper class="swiper" :options="swiperOption">
              <swiper-slide
                v-if="detail.Picture.PictureUrl1"
                class="swiper-img"
                :style="{ 'background-image': 'url(' + detail.Picture.PictureUrl1 + ')' }"
              ></swiper-slide>
              <swiper-slide
                v-if="detail.Picture.PictureUrl2"
                class="swiper-img"
                :style="{ 'background-image': 'url(' + detail.Picture.PictureUrl2 + ')' }"
              ></swiper-slide>
              <swiper-slide
                v-if="detail.Picture.PictureUrl3"
                class="swiper-img"
                :style="{ 'background-image': 'url(' + detail.Picture.PictureUrl3 + ')' }"
              ></swiper-slide>

              <div class="swiper-pagination" slot="pagination"></div>
              <div
                class="swiper-button-prev"
                slot="button-prev"
                v-if="detail.Picture.PictureUrl2"
              ></div>
              <div
                class="swiper-button-next"
                slot="button-next"
                v-if="detail.Picture.PictureUrl2"
              ></div>
            </swiper>
          </div>
        </div>
      </div>
      <div class="sub">
        <h3 v-if="detail.Description || detail.DescriptionDetail">景點介紹</h3>
        <pre v-if="detail.DescriptionDetail">{{
          detail.DescriptionDetail.replaceAll(['。'], '。\n\n')
        }}</pre>
        <pre v-if="!detail.DescriptionDetail && detail.Description">{{
          detail.Description.replaceAll(['。'], '。\n\n')
        }}</pre>

        <div class="nearby-title">
          <router-link
            :to="{
              path:
                '/scenicspot' +
                '/Taiwan/@' +
                detail.Position.PositionLat +
                ', ' +
                detail.Position.PositionLon,
            }"
          >
            <h3>鄰近的景點 <span class="icon icon-right icon-link"></span></h3>
          </router-link>
        </div>
        <div class="container scenicspot-results-container">
          <div
            class="scenicspot-results-wrapper"
            v-for="result in nearbyScenicspot"
            :key="result.ScenicSpotID"
          >
            <router-link
              :to="{
                path:
                  '/information/' +
                  'scenicspot' +
                  '/' +
                  $route.params.city +
                  '/' +
                  result.ScenicSpotID,
              }"
            >
              <div class="scenicspot-results-img-container">
                <div
                  class="scenicspot-results-img"
                  :style="{ 'background-image': 'url(' + result.Picture.PictureUrl1 + ')' }"
                ></div>
              </div>
              <div class="scenicspot-results-content-container">
                <h3>{{ result.Name }}</h3>
                <!-- <p>{{ result.OpenTime }}</p> -->
                <!-- <p>{{ result.TicketInfo }}</p> -->
                <p v-if="result.Address"><span class="icon-address"></span>{{ result.Address }}</p>
              </div>
            </router-link>
          </div>
        </div>
        <div class="nearby-title">
          <router-link
            :to="{
              path:
                '/restaurant' +
                '/Taiwan/@' +
                detail.Position.PositionLat +
                ', ' +
                detail.Position.PositionLon,
            }"
          >
            <h3 v-if="nearbyRestaurant.length !== 0">
              鄰近的飲食 <span class="icon icon-right icon-link"></span>
            </h3>
          </router-link>
        </div>
        <div class="container restaurants-container">
          <div
            class="restaurants-item"
            v-for="restaurant in nearbyRestaurant"
            :key="restaurant.RestaurantID"
          >
            <router-link
              :to="{
                path:
                  '/information/' +
                  'restaurant' +
                  '/' +
                  $route.params.city +
                  '/' +
                  restaurant.RestaurantID,
              }"
            >
              <div
                class="bgi"
                :style="{
                  'background-image': 'url(' + restaurant.Picture.PictureUrl1 + ')',
                }"
              ></div>
              <h2 class="title">{{ restaurant.Name }}</h2>
              <p class="time"><span class="icon icon-open-time"></span>{{ restaurant.OpenTime }}</p>
              <p class="address">
                <span class="icon icon-address"></span> {{ restaurant.Address }}
              </p>
            </router-link>
          </div>
        </div>
        <div class="nearby-title">
          <router-link
            :to="{
              path:
                '/hotel' +
                '/Taiwan/@' +
                detail.Position.PositionLat +
                ', ' +
                detail.Position.PositionLon,
            }"
          >
            <h3>鄰近的旅宿 <span class="icon icon-right icon-link"></span></h3>
          </router-link>
        </div>
        <div class="container hotels-container">
          <div class="hotels-item" v-for="hotel in nearbyHotel" :key="hotel.HotelID">
            <router-link
              :to="{
                path: '/information/' + 'hotel' + '/' + $route.params.city + '/' + hotel.HotelID,
              }"
            >
              <div
                class="bgi"
                :style="{
                  'background-image': 'url(' + hotel.Picture.PictureUrl1 + ')',
                }"
              ></div>
              <div class="content">
                <h2 class="title">{{ hotel.Name }}</h2>
                <p class="address"><span class="icon icon-address"></span>{{ hotel.Address }}</p>
              </div>
            </router-link>
          </div>
        </div>
        <div class="nearby-title">
          <router-link
            :to="{
              path:
                '/activity' +
                '/Taiwan/@' +
                detail.Position.PositionLat +
                ', ' +
                detail.Position.PositionLon,
            }"
          >
            <h3 v-if="nearbyActivity.length !== 0">
              鄰近的活動 <span class="icon icon-right icon-link"></span>
            </h3>
          </router-link>
        </div>
        <div class="container activities-container">
          <div v-for="activity in nearbyActivity" :key="activity.ActivityID" class="activity-item">
            <router-link
              :to="{
                path:
                  '/information/' +
                  'activity' +
                  '/' +
                  $route.params.city +
                  '/' +
                  activity.ActivityID,
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
                <p class="address"><span class="icon icon-address"></span>{{ activity.Address }}</p>
                <br />
                <p>{{ shortDescription(activity.Description) }}</p>
              </div>
            </router-link>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import { Swiper, SwiperSlide, directive } from 'vue-awesome-swiper';
import tdxService from './TdxService';

// import style (<= Swiper 5.x)
import 'swiper/css/swiper.css';

export default {
  components: {
    Swiper,
    SwiperSlide,
  },
  directives: {
    swiper: directive,
  },
  data() {
    return {
      sample: null,
      detail: null,
      nearbyScenicspot: null,
      nearbyRestaurant: null,
      nearbyHotel: null,
      nearbyActivity: null,
    };
  },
  computed: {
    swiperOption() {
      let autoplay = null;
      let navigation = null;

      if (this.detail.Picture.PictureUrl2) {
        autoplay = {
          delay: 3000,

          disableOnInteraction: false,
        };
        navigation = {
          nextEl: '.swiper-button-next',
          prevEl: '.swiper-button-prev',
        };
      }
      return {
        spaceBetween: 30,
        centeredSlides: true,
        loop: true,
        speed: 1500,
        autoplay,
        pagination: {
          el: '.swiper-pagination',
          clickable: true,
        },
        navigation,
      };
    },
  },
  methods: {
    computedServiceInfo(input) {
      let inputArr = input.split(',');
      inputArr = inputArr.filter(Boolean);
      let result = inputArr.join(' ☑ ');
      result = result.replaceAll('<br>', '');
      return result;
    },
    swiper() {
      return this.$refs.mySwiper.$swiper;
    },
    shortDescription(description) {
      let temp;
      const num = 100;
      if (description.length > num) {
        temp = description.slice(0, num);
        temp += '......';
        return temp;
      }
      return description;
    },
    async getDetail() {
      const details = await tdxService.getDetailByID(
        this.$route.params.category,
        this.$route.params.city,
        this.$route.params.id,
      );
      [this.detail] = details;
    },
    async getNearby(cat) {
      let position;
      if ('Position' in this.detail) {
        position = this.detail.Position;
      } else {
        position = this.detail.PointType;
      }
      const temp = await tdxService.getDetailNearby(cat, this.$route.params.city, position);
      return temp;
    },
    async getNearbyAll() {
      this.nearbyScenicspot = await this.getNearby('scenicspot');
      this.nearbyRestaurant = await this.getNearby('restaurant');
      this.nearbyHotel = await this.getNearby('hotel');
      this.nearbyActivity = await this.getNearby('activity');
    },
  },
  async mounted() {
    await this.getDetail();
    this.getNearbyAll();
    this.swiper.slideTo(3, 1000, false);
  },
  watch: {
    async $route(to, from) {
      console.log(to, from);
      await this.getDetail();
      this.getNearbyAll();
      this.swiper.slideTo(3, 1000, false);
    },
  },
};
</script>
<style lang="scss" scoped>
$blue: #a6cde0;
$darkBlue: #7baebe;
h2,
h3 {
  font-weight: normal;
  font-family: 'SourceHanSerifTC-VF';
  color: $darkBlue;
}
h3 {
  font-size: 18px;
  span {
  }
}
button {
  width: 80px;
  display: inline-flex;
  justify-content: center;
  align-items: center;
  background-color: #fff;
  border: solid 1px $blue;
  padding: 5px;
  border-radius: 10px;
  height: 35px;
  cursor: pointer;
  margin-right: 10px;
  &:hover,
  &.active {
    background-color: $blue;
    color: #fff;
  }
}

.svg-inline--fa {
  font-size: 18px;
  margin-right: 10px;
  path {
    color: $darkBlue !important;
  }
}
.my-swiper-container {
  height: 100%;
  width: 100%;
  .swiper-button-prev,
  .swiper-button-next {
    color: #fff;
    @media (max-width: 768px) {
      display: none;
    }
  }
  .swiper-container,
  .wiper-wrapper {
    width: 100%;
    height: 100%;
  }
  .swiper-img {
    width: 100%;
    height: 100%;
    border-radius: 20px;
    background-size: cover;
    background-position: center;
  }
}

.nearby-title {
  display: flex;
  justify-content: space-between;
  .icon {
    margin-left: 5px;
    width: 15px;
    height: 15px;
    display: inline-flex;
    vertical-align: sub;
  }
}
.container {
  margin-bottom: 40px;
}
@mixin shadow {
  box-shadow: 5px 5px 5px 5px rgba(0, 0, 0, 0.2);
}
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
a {
  .icon {
    width: 18px;
    vertical-align: center;
    display: inline-flex;
    vertical-align: bottom;
    vertical-align: sub;
  }
}
a {
  height: 100%;
}
.icon {
  flex: 0 0 18px;
  height: 18px;
  margin-right: 10px;

  background-size: contain;
  background-position: center;
  background-repeat: no-repeat;
  &-calander {
    background-image: url(../assets/images/icons/calander.svg);
  }
  &-address {
    background-image: url(../assets/images/icons/site.svg);
  }
  &-right {
    margin-left: 2px;
  }
  &-link {
    background-image: url(../assets/images/icons/linkArrow_default.svg);
  }
  &-phone {
    background-image: url(../assets/images/icons/phone.svg);
  }
  &-open-time {
    background-image: url(../assets/images/icons/time.svg);
  }
  &-transportation {
    background-image: url(../assets/images/icons/transportation.svg);
  }
  &-information {
    background-image: url(../assets/images/icons/information.svg);
  }
}
.information-container {
  header {
    background-color: $blue;
    height: 69px;
  }
  .information-wrapper {
    padding: 80px;
    padding-top: 50px;
    padding-bottom: 0;
    @media (max-width: 768px) {
      padding: 50px 20px;
      padding-top: 20px;
    }
    .main {
      display: flex;
      @media (max-width: 768px) {
        flex-direction: column-reverse;
      }
    }
    .images {
      flex: 2 1 500px;
      display: block;
      overflow: hidden;
      min-height: 400px;
      max-height: 100vh;
      background-size: cover;
      background-position: center;
      border-radius: 20px;
      margin-left: 40px;
      @media (max-width: 768px) {
        margin: 0;
        flex: 1 1 250px;
        height: 250px;
      }
    }
    .informations {
      padding: 0px 20px;
      position: relative;
      flex: 1 1 250px;
      @media (max-width: 768px) {
        flex: 1 1 200px;
      }
      h3 {
        color: $darkBlue;
        &::before {
          content: '●';
          position: absolute;
          left: -5px;
          top: 23px;
          font-size: 10px;
        }
      }
      &::before {
        // 左側線
        content: '';
        height: calc(100% - 60px);
        width: 1px;
        left: 0px;
        position: absolute;
        top: 35px;
        border-left: 1px solid $darkBlue;
      }
      .content {
        div {
          margin-bottom: 20px;
          display: flex;
          line-height: 25px;
        }
      }
    }
  }
  .sub {
    padding: 0px 20px;
    display: flex;
    flex-direction: column;
    position: relative;

    pre {
      white-space: pre-wrap;
      line-height: 25px;
      @media (max-width: 768px) {
        margin-top: 0;
      }
    }
  }
  color: $darkBlue;
}
.scenicspot-results-container {
  margin-top: 20px;
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  gap: 20px;
  @media (max-width: 768px) {
    margin: 0;
  }

  .scenicspot-results-header {
    flex: 0 0 100%;
    display: flex;
    align-items: flex-end;
    @media (max-width: 768px) {
      flex-direction: column;
      align-items: flex-start;
      gap: 15px;
    }
    .title {
      display: block;
      h2 {
        font-family: 'SourceHanSerifTC-VF';
        font-size: 40px;
        margin: 0;
        padding: 0;
        font-weight: normal;
        margin-right: 5px;
        @media (max-width: 768px) {
          font-size: 25px;
        }
        display: inline;
      }
      p {
        display: inline;
        font-family: 'SourceHanSerifTC-VF';
        margin: 0;
        margin-right: 12px;
      }
    }
    .buttons-container {
      display: flex;
      gap: 10px;
      button {
        width: 50px;
        display: inline-flex;
        justify-content: center;
        align-items: center;
        background-color: #fff;
        border: solid 1px $blue;
        padding: 5px;
        border-radius: 10px;
        height: 35px;
        cursor: pointer;
        &:hover,
        &.active {
          background-color: $blue;
          color: #fff;
        }
      }
    }
  }
  .scenicspot-results-wrapper {
    overflow: hidden;
    display: flex;
    flex: 1 1 250px;
    flex-direction: column;
    border-radius: 20px;
    @include shadow();
    position: relative;
    p {
      margin: 0;
      margin-bottom: 2px;
      display: flex;
      span {
        width: 18px;
        height: 18px;
        background-size: cover;
        background-position: center;

        &.icon-address {
          background-image: url(../assets/images/icons/site.svg);
        }
      }
    }
    .scenicspot-results-content-container {
      padding: 0 10px;
      padding-bottom: 10px;
    }
    .scenicspot-results-img-container {
      border-radius: 20px;
      width: 100%;
      height: 250px;
      overflow: hidden;
      border-radius: 20px 20px 0 0;
      position: relative;
      &::before {
        border-radius: 20px 20px 0px 0px !important;
        position: absolute;
        left: 0;
        top: 0;
        content: '';
        display: block;
        width: 100%;
        height: 100%;
        border-radius: 20px;
        z-index: 1;

        opacity: 0.7;
        backdrop-filter: contrast(80%);
      }

      &:hover .scenicspot-results-img {
        border-radius: 20px 20px 0 0;

        transform: scale(1.2);
      }

      .scenicspot-results-img {
        transition: all 0.5s;
        border-radius: 20px 20px 0 0;
        width: 100%;
        height: 100%;
        background-size: cover;
        background-position: center;
      }
    }
  }
  .pagination {
    flex: 1 0 100%;
    display: flex;
    align-items: start;
    justify-content: flex-end;
    gap: 10px;

    span {
      width: 20px;
      height: 40px;
      display: flex;
      justify-content: center;
      line-height: 35px;
    }
    button {
      $blue: #7baebe;
      cursor: pointer;
      border: 1px solid $blue;
      border-radius: 10px;
      width: 40px;
      height: 40px;
      background-size: cover;
      background-position: center;
      background-color: #fff;
      display: inline;
      &.selected-page {
        background-color: $blue;
        color: #fff;
      }
      &.left-2,
      &.right-2 {
        @media (max-width: 768px) {
          display: none;
        }
      }
      &.next-page,
      &.prev-page {
        border-radius: 0px;
        border: 0px;
        @media (max-width: 768px) {
          display: none;
        }
      }
      &.prev-page {
        background-image: url(../assets/images/icons/arrowWithBorderLeft_default.svg);
      }
      &.next-page {
        background-image: url(../assets/images/icons/arrowWithBorderRight_default.svg);
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
</style>
