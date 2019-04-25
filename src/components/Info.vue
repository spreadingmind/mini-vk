<template>
  <div>
    <h2>Enter person name in search box</h2>
    <h3> </h3>
  </div>
</template>

<script>
import { EventBus } from "../eventBus";

EventBus.$on("new-person-id", onNewPerson);

function onNewPerson(personId) {
  console.log("New person!", personId);
  EventBus.$data.personId = personId;
}

export default {
  data() {
    return {
      personId: null,
    }
  },
  methods: {
    onNewPerson(personId) {
      console.log("New person!", personId);
    }
  }
};
</script>