<template>
  <div class="hello">
    <!-- a form to enter that will post -->
   
    <form action="javascript:void(0)" id="candy_form">
      <p>Name:</p>
      <br />
      <input type="text" name="name" id="name_id" />
      <p>Description:</p>
      <br />
      <input type="text" name="description" id="description_id" />
      <p>Price:</p>
      <br />
      <input type="text" name="price" id="price_id" />
      <p>Image Url:</p>
      <br />
      <input type="text" name="image" id="url_id" />
      <br />
      <!-- post_candy function runs on click -->
      <input
        id="submitNewCandy"
        type="submit"
        value="Post candy"
        @click="post_candy"
      />
    </form>
    <!-- run the candy object in a for loop -->
    <div id="container" v-for="object in candy" :key="object[0]">
      <div>
      <h1>{{ object[0] }}</h1>
      <h4>{{ object[2] }}</h4>
      <h4>{{ object[4] }}</h4>
      <img :src="object[3]" alt="candy image" />
      <!-- run the delete function on this buttons click -->
      <button @click="delete_candy(object[1])">Delete</button>
      </div>
     
      <!-- a form for the update, the id's are set with the id of the candy -->
      <form action="javascript:void(0)" id="update_form">
        <p>Update Price:</p>
        <input type="text" name="price" value="0" :id="'price' + object[1]" />
        <br />
        <p>Update Name:</p>
        <input type="text" name="name" :id="'name' + object[1]" />
        <br />
        <p>Update Description:</p>
        <input type="text" name="description" :id="'desc' + object[1]" />
        <br />
        <p>Update Image Url:</p>
        <input type="text" name="image" :id="'img' + object[1]" />
        <br />
        <!-- update function on submit -->
        <input
          type="submit"
          value="Update candy"
          @click="patch_candy(object[1])"
        />
      </form>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "HelloWorld",
  data() {
    return {
      // empty candy array
      candy: [],
    };
  },
  mounted() {
    // on page mounted the get candy function will run
    this.get_candy();
  },
  methods: {
    
    post_candy: function () {
      // axios reqeuet to my testing server
      axios
        .request({
          method: "POST",
          url: "http://127.0.0.1:5000/candy",
          headers: {
            "Content-Type": "application/json",
          },
          // take in the new data
          data: {
            name: document.getElementById("name_id").value,
            description: document.getElementById("description_id").value,
            price: document.getElementById("price_id").value,
            image_url: document.getElementById("url_id").value,
          },
        })
        .then((res) => {
          console.log(res);
          document.getElementById("candy_form").reset();
          // push the new candy onto the array
          this.candy.push(res.data);
        })
        .catch((err) => {
          console.log(err);
        });
    },

    patch_candy: function (candyId) {
      axios
        .request({
          method: "PATCH",
          url: "http://127.0.0.1:5000/candy",
          headers: {
            "Content-Type": "application/json",
          },
          // send the json data to the api
          data: {
            id: candyId,
            newPrice: document.getElementById("price" + candyId).value,
            newName: document.getElementById("name" + candyId).value,
            newDesc: document.getElementById("desc" + candyId).value,
            newImg: document.getElementById("img" + candyId).value
          },
        })
        .then((res) => {
          console.log(res.data);
          // reset the form on submit
          document.forms["update_form"].reset();
          // reset the price value to 0
          document.getElementById("price" + candyId).value = 0;
          this.get_candy();


        })
        .catch((err) => {
          console.log(err);
        });
    },

    get_candy: function () {
      axios
        .request({
          method: "GET",
          url: "http://127.0.0.1:5000/candy",
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          this.candy = res.data;
        })
        .catch((err) => {
          console.log(err);
        });
    },

    delete_candy: function (id) {
      axios
        .request({
          method: "DELETE",
          url: "http://127.0.0.1:5000/candy",
          headers: {
            "Content-Type": "application/json",
          },
          data: {
            candyId: id,
          },
        })
        .then((res) => {
          console.log(res);
          this.get_candy();
          
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
};
</script>

<style scoped>
#container {
  display: grid;
  grid-template-columns: 1fr 1fr;
}
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
img {
  height: 150px;
}
</style>
