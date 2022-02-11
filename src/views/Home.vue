<script>
import axios from "axios";

export default {
  data: function () {
    return {
      places: [],
      newPlaceParams: {},
      activePlace: "",
      updatePlaceParams: {},
    };
  },
  created: function () {
    this.placesIndex();
  },
  methods: {
    placesIndex: function () {
      axios.get("/places").then((response) => {
        console.log("Places Index", response.data);
        this.places = response.data;
      });
    },
    placeCreate: function () {
      axios.post("/places", this.newPlaceParams).then((response) => {
        console.log("Place Create", response.data);
        this.places.push(response.data);
        this.newPlaceParams = {};
      });
    },
    placeShow: function (place) {
      this.activePlace = place;
      this.updatePlaceParams = place;
      document.querySelector("#place-details").showModal();
    },
    placeUpdate: function (place) {
      axios.patch(`/places/${place.id}`, this.updatePlaceParams).then((response) => {
        console.log("Place Update:", response.data);
      });
    },
    placeDestroy: function (place) {
      var index = this.places.indexOf(place);
      console.log(index);
      axios.delete(`/places/${place.id}`).then((response) => {
        console.log("Place Destroy:", response.data);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>

<template>
  <div class="home">
    <h1>Places</h1>
    <div v-for="place in places" v-bind:key="place.id">
      <h2>
        {{ place.name }}
      </h2>
      <button v-on:click="placeShow(place)">Click for Address</button>
    </div>
    <div>
      <h1>Add A Place</h1>
      Place:
      <input type="text" v-model="newPlaceParams.name" />
      <br />
      Address:
      <input type="text" v-model="newPlaceParams.address" />
      <br />
      <br />
      <button v-on:click="placeCreate()">Add this Place!</button>
    </div>
    <dialog id="place-details">
      <form method="dialog">
        <p>
          Name:
          <input type="text" v-model="updatePlaceParams.name" />
        </p>
        <p>
          Address:
          <input type="text" v-model="updatePlaceParams.address" />
        </p>
        <div>
          <button v-on:click="placeUpdate(updatePlaceParams)">Update</button>
          |
          <button v-on:click="placeDestroy(activePlace)">Delete</button>
          |
          <button>Close</button>
        </div>
      </form>
    </dialog>
  </div>
</template>

<style></style>
