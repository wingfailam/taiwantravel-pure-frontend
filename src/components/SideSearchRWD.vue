<template>
  <div class="background-container">
    <div class="background-wrapper" @click="closeSearch()"></div>
    <button class="close" @click="closeSearch()">x</button>

    <div class="bar-container" :class="{ active: toggle == 1 }">
      <div class="bar-wrapper">
        <div class="dropdown" @click="toggle = !toggle">
          {{ selectedCityZh }}
          /
          {{ selectedTypeZh }}
        </div>
        <input
          class="search"
          name="taiwantravel-search"
          placeholder="請輸入關鍵詞"
          v-model="queryString"
          @keypress.enter="enterClicked()"
        />
        <router-link :to="{ path: '/' + selectedType + '/' + selectedCity + '/' + queryString }">
          <div class="submit">SEARCH</div>
        </router-link>
      </div>

      <div class="dropdown-container" :class="{ active: toggle == 1 }">
        <h2>選擇區域</h2>
        <button
          @click="selectedCity = defaultCity"
          :class="{ active: selectedCity == defaultCity }"
        >
          台灣
        </button>
        <div class="rwd-tabs-container">
          <div class="radio-container">
            <input
              type="radio"
              class="rwd-tabs-radio"
              name="rwd-tabs"
              v-for="(city, index) in Object.keys(cities)"
              :key="city.id"
              :id="'rwd-tab-' + index"
              :checked="cities[city].Cities.some((el) => el.City == $route.params.city)"
            />

            <label
              v-for="(city, index) in Object.keys(cities)"
              :key="city.id"
              :for="'rwd-tab-' + index"
              class="rwd-tabs-label"
              :id="'label-' + index"
              >{{ cities[city].Zh }}</label
            >
            <div class="cities-container">
              <div class="cities-slide">
                <div class="cities-wrapper" v-for="area in Object.keys(cities)" :key="area.id">
                  <button
                    v-for="cts in cities[area].Cities"
                    :key="cts.City"
                    @click="selectedCity = cts.City"
                    :class="{ active: selectedCity == cts.City }"
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
            @click="selectedType = type.En"
            :class="{ active: selectedType == type.En }"
          >
            {{ type.Zh }}
          </button>
        </div>

        <div class="completion-container">
          <button class="active" @click="toggle = !toggle">OK</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import cities from './Cities';
import types from './Types';

const defaultCity = 'Taiwan';
export default {
  name: 'SideSearchRWD',
  data() {
    return {
      cities,
      types,
      toggle: 0,
      defaultCity,
      selectedCity: this.$route.params.city || defaultCity,
      selectedType: this.$route.params.category || types[0].En,
      queryString: '',
    };
  },
  computed: {
    selectedCityZh() {
      if (this.selectedCity === 'Taiwan') {
        return '台灣';
      }
      let payload = '';
      Object.keys(cities).some((area) => {
        const result = cities[area].Cities.find((el) => el.City === this.selectedCity);
        if (result) {
          payload = result.CityName;
          return true;
        }
        return false;
      });
      return payload;
    },
    selectedTypeZh() {
      return types.find((el) => el.En === this.selectedType).Zh;
    },
  },
  methods: {
    enterClicked() {
      this.$router.push(`/${this.selectedType}/${this.selectedCity}/${this.queryString}`);
      console.log('enterClicked');
    },
    closeSearch() {
      this.$emit('close-search');
    },
  },
  watch: {
    async $route(to, from) {
      console.log(to, from);
      this.queryString = '';
      this.closeSearch();
      if (to.params.city !== from.params.city) {
        this.selectedCity = to.params.city || 'Taiwan';
      }
      if (to.params.category !== from.params.cicategoryty) {
        this.selectedType = to.params.category || '';
      }
    },
  },
};
</script>

<style scoped lang="scss">
.background-container {
  position: fixed;
  top: 0;
  left: -100%;
  width: 100vw;
  height: 100vh;
  display: none;
  background-color: rgba(0, 0, 0, 0.5);
  &.active {
    left: 0;
  }
  .close {
    position: absolute;
    right: 10px;
    top: 80px;
    color: #505050;
    z-index: 6;
    width: 35px;
    height: 30px;
    border-radius: 10px;
    border: 0px;
    background-color: #fff;
    font-size: 20px;
    line-height: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .background-wrapper {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }
  @media (max-width: 768px) {
    display: block;
  }
}
$blue: #a6cde0;
@mixin shadow {
  box-shadow: 5px 5px 5px 5px rgba(0, 0, 0, 0.2);
}

.bar-container {
  @include shadow();
  overflow: hidden;
  position: absolute;
  left: 0;
  top: 100px;
  width: calc(100% - 40px);
  border-radius: 20px;
  $items-height: 40px;

  margin: 20px;
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  transition: all 0.5s;
  &.active {
    box-shadow: -2px -2px 50px 1px rgba(0, 0, 0, 0.2);

    .bar-wrapper {
      transition: all 0.5s;
      @media (max-width: 768px) {
        padding-bottom: 300px;
      }
    }
  }
  .bar-wrapper {
    display: flex;
    flex-direction: column;
    justify-content: space-evenly;
    padding: 20px;
    border-radius: 20px;
    background-color: #fff;
    width: calc(100% - 40px);

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
      width: calc(100% - 40px);
      flex: 0 1 $items-height;
      line-height: $items-height;
      border-radius: 10px;
      background-color: #ebebeb;
      color: #707070;
      font-size: 18px;
      z-index: 3;
      padding: 0px 20px;
    }
    .dropdown {
      cursor: pointer;
      position: relative;
      display: flex;
      align-items: center;
      z-index: 4;
    }
    .search {
      border: 0px;
      &:focus {
        outline: none;
      }
    }
    .submit {
      flex: 1 1 $items-height;
      line-height: $items-height;
      border-radius: 10px;
      background-color: #e8cb89;
      color: #fff;
      text-align: center;
      cursor: pointer;
      padding: 0 10px;
      z-index: 3;
      width: calc(100% - 20px);
    }
    &::before {
      position: absolute;
      top: 0px;
      left: 0;
      content: '';
      background-color: #fff;
      width: 100%;
      height: 60px;
      z-index: 4;
    }
  }

  .dropdown-container {
    left: calc(20px - 5px);
    top: calc(-100% - 200px);
    transition: all 0.5s;
    &.active {
      animation-name: test;
      animation-duration: 1s;
      top: 65px;
      @include shadow();
    }
    @keyframes test {
      0% {
        top: calc(-100% - 180px);
        box-shadow: 0px 0px 0px 0px rgba(0, 0, 0, 0.2);
      }
      50% {
        top: 65px;
        box-shadow: 0px 0px 0px 0px rgba(0, 0, 0, 0.2);
      }
      100% {
        @include shadow();
      }
    }
    padding: 0px 20px;
    padding-bottom: 20px;

    position: absolute;

    width: calc(100% - 80px + 10px);
    background-color: #fff;
    border-radius: 20px;
    z-index: 3;
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
    .rwd-tabs-container {
      width: 100%;
      .radio-container {
        display: block;
        width: 100%;

        .cities-container {
          width: 100%;
          height: 145px;
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
</style>
