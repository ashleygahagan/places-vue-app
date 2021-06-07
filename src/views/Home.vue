<template>
  <div class="home">
    <h2>New Place</h2>
    <ul>
      <li v-for="error in errors" v-bind:key="error">{{ error }}</li>
    </ul>
    Name:
    <input type="text" v-model="newPlaceName" />
    <br />
    Address:
    <input type="text" v-model="newPlaceAddress" />
    <br />
    <button v-on:click="createPlace()">Create Place</button>

    <div v-for="place in places" v-bind:key="place.id">
      <h3>Name: {{ place.name }}</h3>
      <p>Address: {{ place.address }}</p>
      <button v-on:click="showPlace(place)">More</button>
    </div>
    <dialog id="place-details">
      <form method="dialog">
        <h1>Place Info</h1>
        <p>
          Name:
          <input type="text" v-model="currentPlace.Name" />
        </p>
        <p>
          Address:
          <input type="text" v-model="currentPlace.Address" />
        </p>
        <p>
          <button v-on:click="updatePlace()">Update</button>
          <button v-on:click="destroyPlace()">Delete</button>
          <button>Close</button>
        </p>
      </form>
    </dialog>
  </div>
</template>

<style></style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      places: [],
      newPlaceName: "",
      newPlaceAddress: "",
      currentPlace: {},
      errors: [],
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("http://localhost:3000/places").then((response) => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    createPlace: function () {
      var params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress,
      };
      axios
        .post("http://localhost:3000/places", params)
        .then((response) => {
          console.log("Success!", response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function (place) {
      console.log(place);
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function () {
      console.log(this.currentPlace);
      var updatePlaceParams = {
        name: this.currentPlace.name,
        address: this.currentPlace.address,
      };
      axios
        .patch(`http://localhost:3000/places/${this.currentPlace.id}`, updatePlaceParams)
        .then((response) => {
          console.log("Success!", response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
    destroyPlace: function () {
      axios.delete(`http://localhost:3000/places/${this.currentPlace.id}`).then((response) => {
        console.log("Success!", response.data);
        var index = this.places.indexOf(this.currentPlace);
        console.log(index);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>
