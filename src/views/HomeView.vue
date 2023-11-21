<template>
    <main>
  
   <header class= "title">
        <h1> Davids feta Hamburgare </h1>
    </header>


    <section> 
    <h2>Välj din favorit</h2>
    <div class="wrapper">
      <Burger v-for="burger in burgers"
              v-bind:burger="burger" 
              v-bind:key="burger.name"
              v-on:orderedBurger="addToOrder($event)"/>
    </div>          
    </section>

    <!--<section id="blackwhite">
    <div class="wrapper"> 
    <div class="burger a">

       
    <h4>Mumsiga burgaren</h4>
    <img src="https://s7d1.scene7.com/is/image/mcdonalds/mcd-sv-products-burgers-glutenfreehamburger-NEW:1-3-product-tile-desktop?wid=829&hei=515&dpr=off" alt="Span" title="Another in-line element" style="width: 300px;">
    <ul>
        <li>Micrad Högrev</li>
        <li><span class="glutenfritt">Glutenfritt</span> bröd</li>
        <li>Diverse goda grönsaker</li>
    </ul>
    

    </div>  

    <div class="burger b"> 
           
        <h4>Mega Mumsiga Burgaren</h4>
        <img src="https://s7d1.scene7.com/is/image/mcdonalds/mcd-sv-products-burgers-glutenfreenbigmac-NEW:1-3-product-tile-desktop?wid=829&hei=515&dpr=off" alt="Span" title="Another in-line element" style="width: 300px;">
        <ul>
            <li>Blandfärs</li>
            <li>Ej <span class="glutenfritt">Glutenfritt</span> bröd</li>
            <li>Diverse goda grönsaker och bacon</li>
        </ul>
        

        </div>
        
        <div class="burger c"> 

            <h5>Mega Mycket Mums på en Burgare</h5>
            <img src="https://s7d1.scene7.com/is/image/mcdonalds/mcd-sv-products-burgers-hamburger-NEW:1-3-product-tile-desktop?wid=829&hei=515&dpr=off" alt="Span" title="Another in-line element" style="width: 300px;">
            <ul>
                <li>Typ som en donken cheeseburgare fast lite bättre</li>
                <li>Ej <span class="glutenfritt">Glutenfritt</span> bröd</li>
                <li>OST!!!</li>
            </ul>
         
    </div>
    </div> 
    </section> -->
    

    <section>
    <h2>Customer Information</h2>
    

       <h4>Dellivery Information </h4>
        <p>
            <label for="name">Full name</label><br>
            <input type="text" id="name" v-model="fn" required="required" placeholder="Full name">
        </p>

        <p>
            <label for="Email">Email</label><br>
            <input type="email" id="email" v-model="em" required="required" placeholder="E-mail address">
        </p>
       
        <p>
            <label for="Payment Methods">Payment Methods</label><br> 
            <select id="Payment Methods" v-model="payment">
                <option>Kontant</option>
                <option>Swisch</option>
                <option>Helst inte kort</option>
            </select>
         </p>
        <p>
          <label for="Gender">Gender:</label><br>

          <input type="radio" id="Male" v-model="gender" value="Male" checked>
          <label for="Male">Male</label><br>

          <input type="radio" id="Female" v-model="gender" value="Female">
          <label for="Female">Female</label><br>

          <input type="radio" id="NoneOfTheAbove" v-model="gender" value="None of the above">
          <label for="NoneOfTheAbove">None of the above</label><br>
        </p>
        <h2>Click where you want the order to be delivered</h2> 
        <div class="map-container">
        
        
        <div id="map" v-on:click="setLocation">
        
        <div id="dots"> <div v-bind:style="{left: location.x + 'px', top: location.y + 'px'}">
        T 
        </div> 
        </div>
        </div>
        </div>

    </section>

    
      
      
   

    <div id="orders">
      <button type="submit" v-on:click="markDone()">
            <img src="https://hips.hearstapps.com/hmg-prod/images/2024-ferrari-812-gts-101-64caae4038b21.jpeg?crop=0.526xw:0.701xh;0.137xw,0.299xh&resize=768:*" style="width: 70px;">
            Send my order ultra mega quick!
          </button>
    </div>

    </main>

    <hr>
    <footer>Copy Right: Davids Feta hamburgare AB</footer>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io();


function MenuItem(productName, image, kCal, gluten, lactose) {
    this.productName = productName; 
    this.image = image;
    this.kCal = kCal;
    this.gluten = gluten;
    this.lactose = lactose;
}

const burgerMenu = menu.map(item => new MenuItem(item.productName, item.image, item.kCal, item.lactose, item.gluten));


export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: burgerMenu, 
      payment: 'Kontant',
      gender: 'Male', 
      orderedBurgers:{},
      location: { x: 0,
            y: 0
          }
      
      /*[ 
        new MenuItem("small burger", "burger1.jpg", 250, false, false),;
        new MenuItem("standard burger", "burger2.jpg", 450, true, false),
        new MenuItem("large burger", "burger3.jpg", 850, false, true)
      ]*/
    }
  },
  methods: {
    addToOrder: function (event) {
    this.orderedBurgers[event.name] = event.amount;
  },

    getOrderNumber: function () {
      return Math.floor(Math.random()*1000);
    },
    setLocation: function (event) {
      this.location.x=event.clientX -80 - event.currentTarget.getBoundingClientRect().left;
      this.location.y=event.clientY -10 - event.currentTarget.getBoundingClientRect().top;
    },
    addOrder: function (event) {

    var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};

      this.location.x=event.clientX -80 - offset.x;
      this.location.y=event.clientY -10 - offset.y;
                 
    },
    markDone: function() {
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: this.location.x,
                                          y: this.location.y },
                               orderItems: this.orderedBurgers,
                               customerDetails: {name: this.fn, email: this.em, gender: this.gender, payment: this.payment}
                              }
                              );
                            
                 
        const orderDetails = [
        this.fn,
        this.em,
        this.payment,
        this.gender,
        JSON.stringify(this.orderedBurgers)
        ]
    console.log(orderDetails)
    }
  }
  }



</script>

<style>
  #map {
    background:url('../../public/img/polacks.jpg');
    width:1920px;
    height:1078px;

    /*background-color: red;*/
  }

  .map-container {
    height: 500px;
    width: 90%; 
    overflow: scroll;
    margin-bottom: 20px;
  }

  body {
    font-family: Arial, Helvetica, sans-serif;
    font-size:large ;
    font-style:oblique;
 }

 .glutenfritt {
    font-weight: bold;
}

#blackwhite {
    color: red;
    background-color:black;
}

button:hover {
    color: green;
    cursor:pointer;
}
.button {
    margin: 20px 0 20px 0; 
}


header {
    background-image: url(https://oskarshamn.com/wp-content/uploads/2020/04/McDonalds-logga.jpg);
    background-size: cover;
    background-repeat: no-repeat;
    height: 250px;
    width: 100%;
}
 /*header.title img {
    opacity: 0.5;
} */

div {
    margin-left: 70px;
}
section {
    border: 5px inset rgb(44, 117, 22); 
    border-radius: 25px;
    margin-top: 10px;
    margin-left: 5px;
    margin-right: 5px;
    padding-left:10px;
    margin-bottom: 20px;
    padding-bottom: 20px;
}

.wrapper {
    display: grid;
    grid-gap: 10px;
    grid-template-columns: 30% 30% 30%;
    color: green;
  
}
 #dots div {
    position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
  }

@media screen and (max-width: 800px) {
   .wrapper {
      display: grid;
      grid-template-columns:20% 20% 20%;
   }}


</style>