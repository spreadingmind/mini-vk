<template>
  <div id="user-info">
    <div id="infoWindow" v-if="dataReady"> <ProfileCard :userInfo="userInfo"/></div>
    <div id="infoWindow" v-else-if="error">{{ error }}</div>
    <div id="infoWindow" v-else>Enter person name in search box</div>
  </div>
</template>

<script>
import {
  EventBus,
  NEW_PERSON_EVENT,
  USER_NOT_FOUND_EVENT,
  EMPTY_INPUT_EVENT
} from "../eventBus";
import { VK_API_VERSION, PROFILE_FIELDS } from "../constants";

import ProfileCard from "./ProfileCard.vue";
const DEFAULT_IMAGE_URL = 'https://i.kym-cdn.com/entries/icons/mobile/000/013/564/doge.jpg';
export default {
  components: {
    ProfileCard
  },
  data() {
    return {
      error: null,
      personId: null,
      userInfo: null,
      dataReady: false
    };
  },
  created() {
    let self = this;
    EventBus.$on(NEW_PERSON_EVENT, function onNewPerson(personId) {
      self.personId = personId;
      return self.getPersonInfo(self.personId);
    });
    EventBus.$on(EMPTY_INPUT_EVENT, function onEmptyInput() {
      self.error = "Empty input";
      self.dataReady = false;
    });
    EventBus.$on(USER_NOT_FOUND_EVENT, function onUserNotFound(person) {
      self.error = `User ${person} was not found`;
      self.dataReady = false;
    });
  },
  methods: {
    getPersonInfo() {
      let self = this;
      if (this.personId) {
        return VK.api(
          "users.get",
          {
            user_ids: [this.personId],
            v: VK_API_VERSION,
            fields: PROFILE_FIELDS
          },
          function(res) {
            if (res.response.length) {
              const userInfo = res.response[0];
              self.userInfo = self.parseInfo(userInfo);
              self.dataReady = true;
            }
          }
        );
      }
      return null;
    },
    parseInfo(userInfo) {
      return {
        firstName: userInfo.first_name,
        lastName: userInfo.last_name,
        online: userInfo.online === 0 ? "offline" : "online",
        sex: userInfo.sex === 2 ? "male" : "female",
        birthDate: userInfo.bdate,
        about: userInfo.about
          ? userInfo.about.replace(/(\r\n|\n|\r)/gm, " ")
          : "no description",
        city: userInfo.city ? userInfo.city.title : '?',
        country: userInfo.country ? userInfo.country.title : '?',
        photo:
          userInfo.has_photo && userInfo.photo_200_orig
            ? userInfo.photo_200_orig
            : DEFAULT_IMAGE_URL,
        freindsCount: userInfo.counters.friends,
        followersCount: userInfo.counters.followers,
        groupCount: userInfo.counters.groups ? userInfo.counters.groups : '?'
      };
    }
  }
};
</script>