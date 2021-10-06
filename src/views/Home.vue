<template>
  <div class="home">
    <h1>New Place</h1>
    <div>
      Title:
      <input type="text" v-model="newPlaceParams.name" />
      <br />
      Address:
      <input type="text" v-model="newPlaceParams.address" />
      <br />
      Image URL:
      <input type="text" v-model="newPlaceParams.image_url" />
      <br />
      <button v-on:click="createPlace()">Create Place</button>
    </div>

    <h1>{{ message }}</h1>
    <div v-for="place in places" v-bind:key="place.id">
      <h2>{{ place.name }}</h2>
      <p>{{ place.address }}</p>
      <button v-on:click="showPlace(place)">Show Details</button>
      <dialog id="place-details">
        <form method="dialog">
          <h2>
            {{ place.name }}
            <input type="text" v-model="currentPlace.name" />
          </h2>
          <p>
            {{ place.address }}
            <input type="text" v-model="currentPlace.address" />
          </p>
          <p>
            Image:
            <input type="text" v-model="currentPlace.image_url" />
            <img class="center" :src="currentPlace.image_url" alt="" />
          </p>
          <br />
          <button v-on:click="updatePlace(currentPlace)">Update</button>
          <button v-on:click="destroyPlace()">Delete</button>
          <button>Close</button>
        </form>
      </dialog>
    </div>
  </div>
</template>

<style>
.center {
  display: block;
  margin-left: auto;
  margin-right: auto;
  width: 40%;
  height: 40%;
}
</style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      message: "Places I've Been",
      places: [],
      currentPlace: {},
      newPlaceParams: {},
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("http://localhost:3000/places").then((response) => {
        console.log(response);
        this.places = response.data;
      });
    },
    createPlace: function () {
      axios.post("http://localhost:3000/places", this.newPlaceParams).then((response) => {
        console.log(response);
        this.places.push(response.data);
        this.newPlaceParams = {};
      });
    },
    showPlace: function (place) {
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function (place) {
      let editPlaceParams = place;
      axios
        .patch(`http://localhost:3000/places/${place.id}`, editPlaceParams)
        .then((response) => {
          console.log(response);
          this.currentPlace = {};
        })
        .catch((error) => {
          console.log(error.response);
        });
    },
    destryPlace: function (place) {
      axios.delete(`http://localhost:3000/places/${place.id}`).then((response) => {
        console.log(response);
        this.places.splice(this.places.indexOf(place), 1);
      });
    },
  },
};
</script>
