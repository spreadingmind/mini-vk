<template>
  <div id="app">
    <div id="logo">
      <a href="https://mini-vk.herokuapp.com/">
        <img alt="VK logo" src="./assets/vk_logo.png" width="100" height="100">
      </a>
      <span id="caption" style>mini version</span>
    </div>
    <div class="wrap">
      <div class="search">
        <input
          id="search-field"
          type="text"
          name="search"
          v-model="person"
          v-on:keyup.enter="searchPerson"
          placeholder="Search person.."
        >
      </div>
    </div>
    <div id="page-tabs">
      <button
        id = 'tab-button'
        v-for="tab in tabs"
        :key="tab"
        @click="selected = tab;"
        :class="['tab-btn', { active: selected === tab }]"
      >{{ tab }}</button>
      <component :is="selected" class="tab"></component>
    </div>
  </div>
</template>

<script>
import Friends from "./components/Friends.vue";
import Info from "./components/Info.vue";
import {
  EventBus,
  NEW_PERSON_EVENT,
  USER_NOT_FOUND_EVENT,
  EMPTY_INPUT_EVENT
} from "./eventBus";
import { VK_API_VERSION } from "./constants";

export default {
  name: "app",
  components: {
    Friends,
    Info
  },
  data() {
    return {
      tabs: ["Info", "Friends"],
      selected: "Info",
      person: "",
      personId: null
    };
  },
  methods: {
    searchPerson() {
      let self = this;
      if (!this.person) {
        EventBus.$emit(EMPTY_INPUT_EVENT);
        return;
      }
      return VK.api(
        "users.search",
        { q: this.person, sort: 0, v: VK_API_VERSION },
        function(res) {
          if (!res.response) {
            EventBus.$emit(USER_NOT_FOUND_EVENT, self.person);
            return;
          }
          if (res.response.count === 0) {
            EventBus.$emit(USER_NOT_FOUND_EVENT, self.person);
            return;
          }
          if (res.response) {
            // just taking first most popular person
            self.personId = res.response.items[0].id;
            EventBus.$emit(NEW_PERSON_EVENT, self.personId);
            return;
          }
        }
      );
    }
  }
};
</script>

<style>
@import url("https://fonts.googleapis.com/css?family=Lora");

#app,
button,
input {
  font-family: "Lora", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 80px;
  font-weight: bold;
}

body,
html {
  height: 100%;
  margin: 0;
}

#logo {
  position: absolute;
  top: 0;
  left: 0;
  margin: 10px;
}
#caption {
  display: block;
}

input[type="text"] {
  position: absolute;
  top: 1;
  right: 0;
  margin: 10px;
  width: 200px;
  box-sizing: border-box;
  border: 2px solid #ccc;
  border-radius: 4px;
  font-size: 16px;
  background-color: white;
  background-position: 10px 10px;
  background-repeat: no-repeat;
  padding: 12px 20px 12px 40px;
  transition: width 0.4s ease-in-out;
}

input[type="text"]:focus {
  width: 70%;
}

.tab-btn {
  padding: 6px 10px;
  background-color: #4a76a8;
  color: white;
  float: left;
  cursor: pointer;
  margin-bottom: 1rem;
  border: 1px solid #cccccc;
  outline: none;
  font-size: 17px;
  width: 50%;
}

.active {
  background-color: #335e8f;
}

.tab {
  margin: auto;
  padding: 50px;
}
</style>
