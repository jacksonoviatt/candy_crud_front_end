<template>
  <div class="hello">
    <!-- <div>{{candy}}</div> -->
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
      <input
        id="submitNewCandy"
        type="submit"
        value="Post candy"
        @click="post_candy"
      />
    </form>
    <div id="container" v-for="object in candy" :key="object[0]">
      <h1>{{ object[0] }}</h1>
      <h4>{{ object[2] }}</h4>
      <h4>{{ object[4]}}</h4>
      <img :src="object[3]" alt="candy image" />
      <br />
      <button @click="delete_candy(object[1])">Delete</button>
      <form action="javascript:void(0)" class="candy_form">
        <p> Update Price: </p>
        <input type="text" name="price" :id="'price' + object[1]" />
        <br>
        <input
        type="submit"
        value="Update candy"
        @click="patch_candy(object[1])"
      />
      </form>
    </div>
    <br />
    <button @click="get_candy()">Click me!</button>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "HelloWorld",
  data() {
    return {
      candy: [],
    };
  },
  mounted() {
    this.get_candy();
  },
  methods: {
    post_candy: function () {
      axios
        .request({
          method: "POST",
          url: "http://127.0.0.1:5000/candy",
          headers: {
            "Content-Type": "application/json",
          },
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
          data: {
            id: candyId,
            newPrice: document.getElementById("price" + candyId).value,
          },
        })
        .then((res) => {
          console.log(res.data);
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
          // document.getElementById("container").reset();
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
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
