<template>
  <div id="profile-card">
    <div id="card">
      <img v-bind:src="userInfo.photo" alt="Photo" width="300" height="300">
      <h1 v-if="userInfo.online">
        {{ userInfo.firstName }} {{ userInfo.lastName }}
        <div id="online">online</div>
      </h1>
      <h1 v-else>
        {{ userInfo.firstName }} {{ userInfo.lastName }}
        <div id="offline">offline</div>
      </h1>
      <p class="title">{{ userInfo.about }}</p>
      <p>sex: {{ userInfo.sex }}</p>
      <p>birth date: {{ userInfo.birthDate }}</p>
      <p>from: {{ userInfo.city}}, {{userInfo.country }}</p>
      <div
        id="statistics"
      >friends:{{ userInfo.freindsCount}} followers:{{ userInfo.followersCount }} groups:{{ userInfo.groupCount }}</div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ProfileCard",
  props: ["userInfo"]
};
</script>


<style scoped>
img {
  max-width: 300px;
  max-height: 500px;
  width: auto;
  height: auto;
}

#online {
  color: forestgreen;
  font-size: 15px;
}
#offline {
  color: red;
  font-size: 15px;
}
#statistics {
  word-spacing: 2em;
}
.card {
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
  max-width: 300px;
  margin: auto;
  text-align: center;
}

.title {
  color: grey;
  font-size: 18px;
}
</style>
