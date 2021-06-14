<template>
  <div id="app">
    <h1></h1>
    <div>
      <!-- Form that takes in user input. -->
      <form action="javascript:void(0)" id="candy_form">
        <div class="addCandyContainer"> 
          <div><h2>Add a candy</h2></div>
          <div class="gridCandy">
            <input id="candy_name" type="text" placeholder="Name">
            <input id="candy_description" type="text" placeholder="Description">
            <input id="candy_price" type="text" placeholder="Price">
            <input id="candy_img" type="text" placeholder="Image URL">
            <!-- This cllick event adds a candy -->
            <input @click="post_candy" type="submit" value="Submit">
          </div>
        </div> 
      </form>
    </div>
    
    <div >
      <!-- Loop to retrieve all candy data and display it on the page. -->
      <div v-for="candy in candies" :key="candy[4]" class="candyContainer">
        <!-- Form that takes in user input. -->
        <form action="javascript:void(0)">
          <h2>Edit Candy</h2>
          <input type="text" :id="'name'+candy[4]" placeholder="Candy name" :value="candy[0]"/>
          <input type="text" :id="'desc'+candy[4]" placeholder="Description" :value="candy[1]"/>
          <input type="text" :id="'price'+candy[4]" placeholder="Price" :value="candy[2]"/>
          <input type="text" :id="'image'+candy[4]" placeholder="Enter a url for your image" :value="candy[3]"/>
          <!-- This click event edits candy information -->
          <button @click="edit_candy(candy[4])">Save</button>
        </form>
        <div>
          <img :src="candy[3]" :alt="candy[1]">
          <!-- This click event deletes a candy -->
          <button @click="delete_candy(candy[4])">Delete</button>
          <h2>{{candy[0]}}</h2>
          <h2>{{candy[1]}}</h2>
          <h2>{{candy[2]}}</h2>
        </div>
      </div>
    </div>
  </div>
</template>
 
<script>
import axios from 'axios'
 
export default {
  name: 'App',
  data() {
    return {
      // This array stores each candy element.
      candies: [],
    }
  },
  mounted () {
      // This method gets all the candy data.
      axios.request({
        url: "http://127.0.0.1:5000/candy",
        method: "GET"
      }).then((res) => {
        this.candies = res.data;
        console.log(res)
      }).catch((err) => {
        console.log(err)
      });
  },
  methods: {
    // This method allows the user to add a new post.
    post_candy() {
      axios.request({
        url: "http://127.0.0.1:5000/candy",
        method: "POST",
        headers: {
          "Content-Type": "application/json"
        },
        // The required data properties are sent with specific values inputted by the user.
        data: {
          name: document.getElementById("candy_name").value,
          description: document.getElementById("candy_description").value,
          price: document.getElementById("candy_price").value,
          image: document.getElementById("candy_img").value
        },
      }).then((res) => {
        document.getElementById("candy_form").reset();
        console.log(res);
        this.candies.push(res.data);
      }).catch((err) => {
        console.log(err);
      });
    },
    // This request allows the user to delete a candy.
    delete_candy(candy_id) {
      axios.request({
        url: "http://127.0.0.1:5000/candy",
        method: "DELETE",
        headers: {
          "Content-Type": "application/json"
        },
        data: {
          id: candy_id
        },
      }).then((res) => {
        let candies = [];
        // Conditional to delete a candy from an array of candies.
        for(let candy of this.candies){
          if (candy[4] != candy_id){
            candies.push(candy);
          }
        }
        this.candies = candies;
        console.log(res);
      }).catch((err) => {
        console.log(err);
      });
    },

    // This request allows the user to edit a candy.
    edit_candy(candy_id) {
      axios.request({
        url: "http://127.0.0.1:5000/candy",
        method: "PATCH",
        headers: {
          "Content-Type": "application/json"
        },
        // The required data properties are sent with specific values inputted by the user.
        // Using template literals to get the candy id.
        data: {
          name: document.getElementById(`name${candy_id}`).value,
          description: document.getElementById(`desc${candy_id}`).value,
          price: document.getElementById(`price${candy_id}`).value,
          image: document.getElementById(`image${candy_id}`).value,
          id: candy_id
        },
      }).then((res) => {
        console.log(res);
        let candies = [];
        // Loop to get an array of candies.
        // Conditionals that add updated changes to the page.
        for(let candy of this.candies){
          if (candy[4] == candy_id){
            candies.push(res.data[0]);
          } else {
            candies.push(candy);
          }
        }
        this.candies = candies;
      }).catch((err) => {
        console.log(err);
 
      });
    }
  }
};
 
</script>
 
<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

#candy_form {
  background-color: pink;
  max-width: 700px;
  margin: auto;
}

h1 {
  font-size: 3rem;
  background-color: pink;
  background-image: url("https://cdn.vox-cdn.com/thumbor/zRzdodIlRqh__6lOJY8AZAI0cd4=/0x0:1080x810/1400x1050/filters:focal(0x0:1080x810):format(jpeg)/cdn.vox-cdn.com/uploads/chorus_image/image/50583643/gene-wilder-willy-wonka.0.0.jpg");
  background-repeat: no-repeat;
  background-size: cover;
  height: 500px;
}

form {
  display: grid;
  grid-template-columns: 1fr;
  place-items: center;
  row-gap: 11px;
  margin-top: 20px;
}
 
img {
  height: 377px;
}
 
.gridCandy {
  display: grid;
  grid-template-columns: 1fr;
  place-content: center;
   row-gap: 11px;
}

.addCandyContainer {
  display: grid;
  grid-template-columns: 1fr 1fr;
  place-items: center;
}

.candyContainer {
  background-color: aqua;
  width: 700px;
  margin: auto;
}

</style>
 

