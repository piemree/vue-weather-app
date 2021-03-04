<template>
  <div id="app">
    <h2 class="title">Weather App</h2>
    <div class="search-city">
      <input
        class="city-name"
        @keyup="submit($event)"
        type="text"
        placeholder="Enter a city name"
      />
      <div class="select-el">
        <span>{{ selected }}</span>
        <button @click="showList">
          <font-awesome-icon
            v-if="isClicked"
            class="icon1"
            :icon="['fas', 'sort-up']"
          />
          <font-awesome-icon
            v-else
            class="icon2"
            :icon="['fas', 'sort-down']"
          />
        </button>
      </div>
      <transition>
        <ul v-show="isClicked" class="list">
          <li
            @click="changeCountry($event)"
            v-for="(country, i) in countries"
            :key="i"
          >
            <p>{{ country.name }}-{{ country.code }}</p>
          </li>
        </ul>
      </transition>
    </div>
    <div class="info">
      <div class="city-svg">
        <h2>{{ weather.cityName.split(" ")[0] }}</h2>
        <skycon
          v-show="test == '01d'"
          size="128"
          id="skycon"
          color="white"
          condition="clear-day"
        />
        <skycon
          v-show="test == '01n'"
          size="128"
          id="skycon"
          color="white"
          condition="clear-night"
        />
        <skycon
          v-show="test == '02d'"
          size="128"
          id="skycon"
          color="white"
          condition="partly-cloudy-day"
        />
        <skycon
          v-show="test == '02n'"
          size="128"
          id="skycon"
          color="white"
          condition="partly-cloudy-night"
        />
        <skycon
          v-show="
            test == '03d' || test == '03n' || test == '04d' || test == '04n'
          "
          size="128"
          id="skycon"
          color="white"
          condition="cloudy"
        />
        <skycon
          v-show="test == '09d' || test == '09n'"
          size="128"
          id="skycon"
          color="white"
          condition="rain"
        />
        <skycon
          v-show="test == '10d' || test == '10n'"
          size="128"
          id="skycon"
          color="white"
          condition="sleet"
        />
        <skycon
          v-show="test == '11d' || test == '11n'"
          size="128"
          id="skycon"
          color="white"
          condition="wind"
        />
        <skycon
          v-show="test == '13d' || test == '13n'"
          size="128"
          id="skycon"
          color="white"
          condition="snow"
        />
        <skycon
          v-show="test == '50d' || test == '50n'"
          size="128"
          id="skycon"
          color="white"
          condition="fog"
        />
      </div>

      <h3>{{ weather.degree || currentWeather.degree }}°</h3>

      <p>{{ weather.description || currentWeather.description }}</p>
    </div>

    <div v-show="showAlert" class="alert">
      <p>Please check your selections are correct</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      showAlert: false,
      selected: "Turkey-TR",
      isClicked: false,
      test: false,
      currentWeather: {
        cityName: "",
        degree: 0,
        description: "",
        swg: "",
      },
    };
  },
  methods: {
    async submit(e) {
      console.log(e);
      if (e.keyCode == 13) {
        this.$store.dispatch("setCityName", e.target.value);

        try {
          let result = await this.$store.dispatch("getWeather");
        } catch (error) {
          this.showAlert = true;
          setTimeout((params) => {
            this.showAlert = false;
          }, 3000),
            0; //What is this , ı am not sure :)
        }
      }
    },
    changeCountry(e) {
      this.selected = e.target.childNodes[0].innerText;
      this.$store.dispatch("setCountryName", e.target.childNodes[0].innerText);
      this.isClicked = !this.isClicked;
    },
    showList() {
      this.isClicked = !this.isClicked;
    },
  },
  computed: {
    weather() {
      let state = this.$store.getters.getWeather;
      if (state.swg) {
        this.test = state.swg;
      }
      return state;
    },
    countries() {
      return this.$store.getters.getCountries;
    },
  },
  async created() {
    try {
      let result = await this.$store.dispatch("getWeather");
    } catch (error) {
      this.showAlert = true;
      setTimeout((params) => {
        this.showAlert = false;
      }, 3000),
        0; //What is this , ı am not sure :)
    }
  },
};
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
    Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
}

.v-enter-active {
  animation: easy-ın 0.1s;
}
.v-leave-active {
  animation: easy-ın 0.1s reverse;
}

@keyframes easy-ın {
  0% {
    transform: scale(0);
    opacity: 1;
  }

  100% {
    transform: scaleY(1);
    opacity: 1;
  }
}
.title {
  position: absolute;
  top: 1rem;
  color: white;
  font-size: 30px;
}
#app {
  height: 100vh;
  background: rgb(2, 0, 36);
  background: linear-gradient(
    90deg,
    rgba(2, 0, 36, 1) 0%,
    rgba(44, 130, 205, 1) 0%,
    rgba(0, 188, 255, 1) 100%,
    rgba(138, 3, 203, 1) 100%
  );
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  align-items: center;
}
.alert {
  position: absolute;
  top: 2%;
  background-color: rgb(255, 25, 25);
  color: white;
  height: 3rem;
  border-radius: 10px;
  padding: 0 1rem;
  padding-top: 0.6rem;
  font-size: 20px;
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.7);
}

.search-city {
  height: 5rem;
  display: flex;
  width: 40rem;
  justify-content: center;
  background-color: inherit;
  border-bottom: 2px solid white;
  border-radius: 0;
  color: white;
  position: relative;
}
.search-city input {
  height: inherit;
  width: 40rem;
  outline: none;
  margin: auto;
  font-size: 3.5rem;
  background-color: inherit;
  border: none;
  color: white;
  padding: 20px;
  text-align: center;
}
.select-el {
  height: 100%;
  width: 100%;
  display: flex;
  justify-content: space-between;
  padding: 0 2rem;
  align-items: center;
  position: absolute;
  margin-left: 40px;
  bottom: -100%;
  text-align: center;
}
.select-el span {
  text-align: center;
  font-size: 25px;
}
.select-el button {
  background-color: inherit;
  border: none;
  outline: none;
  color: white;
}
.list {
  background-color: inherit;
  border: none;
  text-align: center;
  width: 33rem;
  position: absolute;
  bottom: -450%;
  list-style: none;
  max-height: 2rem;
  min-height: 16rem;
  overflow: scroll;
  overflow-x: hidden;
  display: block;
  transform-origin: top right;
}
.list::-webkit-scrollbar {
  background-color: inherit;
  width: 12px;
  height: fit-content;
}

.list::-webkit-scrollbar-track {
  -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
  border-radius: 10px;
}

.list::-webkit-scrollbar-thumb {
  border: none;
  border-radius: 10px;
  -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.5);
}
.list li {
  width: 100%;
  font-size: 20px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  height: 3rem;
  text-align: center;
  cursor: pointer;
}
.list li:hover {
  border: 1px solid white;
}
.list p {
  pointer-events: none;
}
.icon1 {
  font-size: 3rem;
  padding-top: 12px;
  cursor: pointer;
}
.icon2 {
  font-size: 3rem;
  padding-bottom: 12px;
  cursor: pointer;
}

.search-city option {
  background-color: inherit;
  border: none;
  text-align: center;
  width: 4rem;
  overflow: hidden;
}
::-webkit-input-placeholder {
  /* Edge */
  color: rgb(212, 212, 212);
  font-weight: 300;
}

.info {
  height: 20rem;
  width: 30rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-between;
  padding: 0.5rem 0;
  color: white;
}

.city-svg {
  display: flex;
  justify-content: space-around;
  width: inherit;
  align-items: center;
  height: 10rem;
}
.city-svg h2 {
  font-size: 70px;
}
.info h3 {
  font-size: 50px;
}
.info p {
  font-size: 20px;
}
</style>