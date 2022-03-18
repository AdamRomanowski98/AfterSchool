<template>
  <div id="app">
      <div v-if="showCheckout != true">
        <div id="cartBtn">
          <button v-if='showCart == true' @click="openCheckout()"><span class="fas fa-cart-plus"></span>Cart: {{cartLength}}</button>
          <button disabled="true" v-else><span class="fas fa-cart-plus"></span>Cart: {{cartLength}}</button>
        </div> 

        <br>

        <lesson-component :lesson="lesson" :searchLessons="searchLessons" @addItem="addItem" @searchItem="searchItem"></lesson-component>
      </div>

      <div v-else> <!-- Code for the Checkout Functionality-->
      <div id="cartBtn">
        <button v-if='showCart == true' @click="openCheckout()"><span class="fas fa-cart-plus"></span>Cart: {{cartLength}}</button>
        <button disabled="true" v-else><span class="fas fa-cart-plus"></span>Cart: {{cartLength}}</button>
      </div> 

      <br>

      <checkout-component :cart="cart" :lesson="lesson" @removeItem="removeItem" @emptyCart="emptyCart"></checkout-component>
      </div>
  </div>
</template>

<script>
import CheckoutComponent from './components/Checkout.vue'
import LessonComponent from './components/Lessons.vue'

export default {
  name: 'App',
  components: {
    CheckoutComponent,
    LessonComponent
  },
  data() {
    return {
      lesson: [],
      searchLessons: [],
      showCart: false,
      showCheckout: false,
      orderComplete: false,
      cartLength: 0,
      cart: [], 
    }
  },
  methods:{
    addItem(lesson){
      if(lesson.spaces > 0){
        lesson.spaces -= 1
        this.cart.push(lesson._id) //pushes id to the cart

        for(let i = 0; i < this.lesson.length; i++){
          if(lesson._id === this.lesson[i]._id){
            this.lesson[i].spaces = lesson.spaces;
          }
        }
      }
    },
     emptyCart(){
      this.cart = [];
    },
    removeItem(id){
      for(let i = 0; i < this.cart.length; i++){
        if(id === this.cart[i]){ 
          this.cart.splice(i, 1); 
          break; 
        }
      }
      for(let i = 0; i < this.lesson.length; i++){
        if(id === this.lesson[i]._id){
          this.lesson[i].spaces += 1
        }
      }
    },
    openCheckout(){
      this.showCheckout = this.showCheckout ? false : true;
    },
   
  },
  created: function(){
    fetch('https://afterschoolclub-adamromanowski.herokuapp.com/collection/lessons')
    .then(response => response.json())
    .then(data => (this.lesson = data))
  },
  watch: {
    cart(){
      if (this.cart.length > 0){
        this.showCart = true;
      } else {
        this.showCart = false; 
        this.showCheckout = false; 
      }
      this.cartLength = this.cart.length;
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 0px;
}
</style>
