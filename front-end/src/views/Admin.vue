<template>
<div class="admin">
    <hr>
    <h2>Welcome to the Factory of Love</h2>
    <hr>
    <div class="heading">
      <h3>Create your Item for Valentine</h3>
    </div>
    <div class="add">
      <div class="flex1">
        <div class="form">
          <input v-model="title" placeholder="Item Name">
          <br>
          <input v-model="description" placeholder="Description" type="description">
          <br>
          <input v-model="price" placeholder="Price in $" >
          <br>
          <input style="width: 239px" type="file" name="photo" @change="fileChanged">
          <br>
        </div>
        <button @click="upload">Upload</button>
      </div>
      <div class="flex1">
        <div class="upload" v-if="addItem">
          <div class="things">
          <h2>{{addItem.title}}</h2>
          <h2>{{addItem.description}}</h2>
          <h2>{{addItem.price}}</h2>
          </div>
          <img :src="addItem.path" />
        </div>
      </div>
    </div>

    <div class="heading">
    <h3>Delete or Recreate an Item</h3>
    </div>
    <div class="edit">
      <div class="flex1">
        <div class="form">
          <input v-model="findTitle" placeholder="Search">

          <div class="suggestions" v-if="suggestions.length > 0">
            <div class="suggestion" v-for="s in suggestions" :key="s.id" @click="selectItem(s)">{{s.title}}
            </div>
          </div>
        </div>
      </div>
      <div class="flex1">
        <div class="upload" v-if="findItem">
          <img :src="findItem.path" />
          <div class="contain">
          <div class="padd"></div>  
          <input v-model="findItem.title" placeholder="Item Name" class = "different_button">
          <p></p>
          <input v-model="findItem.description" placeholder="Description" type="description" class = "different_button">
          <p></p>
          <input v-model="findItem.price" placeholder="Price" class = "different_button">
          <p></p>
          </div>
        </div>
      </div>
      <div class="flex1">
        <div class="actions" v-if="findItem">
          <button @click="deleteItem(findItem)" class="different_button">Delete</button>
          <button @click="editItem(findItem)" class="different_button">Edit</button>
        </div>
      </div>
    </div>
    <hr>
  </div>
</template>


<style scoped>
@media only screen and (max-width: 500px) {
  .upload {
    margin-left: 100px !important;
    padding-top:10px !important;
  }

  .form {
    margin: 0px !important;
  }
  .actions {
    margin-left: 100px !important;
  }
}
.image h2 {
  font-size: 1em;
}

.heading {
  display: flex;
  margin-bottom: 20px;
  margin-top: 20px;
  padding-left: 100px;

}

.padd{
  height: 30%;
}

.things{
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  align-content: center;
  align-items: center;
  width: 44%;
}

.things h2{
  margin: 20 0 0 0;
}

.heading h2 {
  margin-top: 8px;
  margin-left: 10px;
  font-size: 25px;
  color: whitesmoke;
}

.add,
.edit {
  display: flex;
  flex-wrap:wrap;
  flex-direction: row;
}

.circle {
  border-radius: 50%;
  width: 18px;
  height: 18px;
  padding: 8px;
  background: #333;
  color: #fff;
  text-align: center
}

.flex1 {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: center;
  align-content: center;
  align-items: center;
}

/* Form */
input,
description,
select,
button {
  font-family: 'Montserrat', sans-serif;
  font-size: 1em;
  margin-left:100px;
}
.upload {
  margin-left: 100px;
  display: flex;
  flex-direction: row;
  
}
.different_button {
  margin-left:0px !important;
}

.add > .flex1 > .form > input{
  margin-top: 10px;
}


/* Uploaded images */
.upload h2 {
  margin: 0px;
  font-size: 15px;
  color: whitesmoke;
  padding:2px;
}

.upload img {
  max-width: 300px;
}

/* Suggestions */
.suggestions {
  width: 200px;
  border: 1px solid #ccc;
  margin-left:100px;
}

.suggestion {
  min-height: 20px;
}

.suggestion:hover {
  background-color: #5BDEFF;
  color: #fff;
}

 h1{
  padding-left: 100px !important;
  color: white;
}

hr{
  height: 4px;
  background-color: darkred;
  border: none;
}

button{
  height: 30px;
  width: 90px;
}

</style>

<script>
import axios from 'axios';
export default {
  name: 'Admin',
  data() {
    return {
      title: "",
      description:"",
      price: "",
      file: null,
      addItem: null,
      items: [],
      findTitle: "",
      findItem: null,
    }
  },

  created() {
    this.getItems();
  },

  computed: {
    suggestions() {
      let items = this.items.filter(item => item.title.toLowerCase().startsWith(this.findTitle.toLowerCase()));
      return items.sort((a, b) => a.title > b.title);
    }
  },

  methods: {

    selectItem(item) {
      this.findTitle = "";
      this.findItem = item;
    },

    async editItem(item) {
      try {
        await axios.put("/api/items/" + item._id, {
          title: this.findItem.title,
          description: this.findItem.description,
          price: this.findItem.price,
        });
        this.findItem = null;
        this.getItems();
        return true;
      } catch (error) {
        console.log(error);
      }
    },

    async deleteItem(item) {
      try {
        await axios.delete("/api/items/" + item._id);
        this.findItem = null;
        this.getItems();
        return true;
      } catch (error) {
        console.log(error);
      }
    },

    async getItems() {
      try {
        let response = await axios.get("/api/items");
        this.items = response.data;
        console.log(this.items)
        return true;
      } catch (error) {
        console.log(error);
      }
    },

    fileChanged(event) {
      this.file = event.target.files[0]
    },

    async upload() {
      try {
        const formData = new FormData();
        formData.append('photo', this.file, this.file.name)
        let r1 = await axios.post('/api/photos', formData);
        let r2 = await axios.post('/api/items', {
          title: this.title,
          description: this.description,
          price: this.price,
          path: r1.data.path
        });
        this.addItem = r2.data;
      } catch (error) {
        console.log(error);
      }
    },
  }
}
</script>


