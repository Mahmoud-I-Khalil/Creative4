<template>
    <div class="container">
        <div class="items" v-for="items in products" :key="items.id">
            <div class="item-image"> 
                <img :src="items.path">
            </div>
            <div class="collab">
                    <div class="name"> {{items.title}} </div>
                    <div class = "descriptionI"> "{{items.description}}"</div>
                    <button class="buy" @click="addToCart(items)">BUY</button>
            </div>
            <div class="description">
                <div class= "price">{{items.price}}$</div>
            </div>
        </div>
    </div>
</template>


<script>
import axios from 'axios';
export default {
  name: 'Store',

  data() {
    return {
      items: [],
    }
  },

  mounted() {
    this.getItems();
  },

  computed: {
    products() {
      return this.items;
    }
  },

  methods:{

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

    addToCart(item) {
      this.$root.$data.cart.push({
        name: item.title,
        description: item.description,
        price: item.price,
        image: item.path,
      })
    },
  }

}
</script>


<style scoped>

.message{
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
    margin: 50px;

}

img{
    height: 300px;
    width: 320px;
    border-radius: 20px;
}

.items{
    flex: 0 0 35%;
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin: 20px 0 20px 0;
}

.items:hover{
    
    background: linear-gradient( rgba(255, 255, 255, 0.25), rgba(255, 255, 255, 0.25) );
    border-radius: 40px;
}

.collab{
    border: 1px solid black;
    text-align: center;
    background: darkred;
    color: white;
    border-radius: 20px;
    padding: 20px;
    justify-content: center;
}
.description{
    display: flex;
    align-items: center;
}

.name{
    font-size: 2em;
}

.buy {
  font-size: .8em;
  background-color: lightcyan;
  border-radius: 6px;
  margin-right: 8px;
  padding: 2px 6px;
}

.buy:hover {
  background-color: lightskyblue;
}



</style>