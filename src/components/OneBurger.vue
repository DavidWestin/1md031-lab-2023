<template>
   <div>
    <h4>{{ burger.productName }}</h4>
    <img v-bind:src="burger.image" alt="Burger Image" style="width: 300px">
    <p>Kalorier: {{ burger.kCal }}</p>
    <p v-if = "burger.gluten">Gluten: Innehåller</p>
    <p v-if = "!burger.gluten">Gluten: Innehåller Ej</p>
    <p v-if = "burger.lactose">Laktos: Innehåller </p>
    <p v-if = "!burger.lactose">Laktos: Innehåller Ej</p>

    <p>Antal: {{this.amountOrdered }}</p>
    <div>
    <button id="decreaseButton" v-on:click="decreaseAmount"> - </button>
    <button id="increaseButton" v-on:click="increaseAmount"> + </button>
    </div>
  </div>
       
    <!--<h4>Mumsiga burgaren</h4>
    <img src="https://s7d1.scene7.com/is/image/mcdonalds/mcd-sv-products-burgers-glutenfreehamburger-NEW:1-3-product-tile-desktop?wid=829&hei=515&dpr=off" alt="Span" title="Another in-line element" style="width: 300px;">
    <ul>
        <li>Micrad Högrev</li>
        <li><span class="glutenfritt">Glutenfritt</span> bröd</li>
        <li>Diverse goda grönsaker</li>
    </ul> -->  

  </template>
  
  <script>
  export default {
    name: 'OneBurger',
    props: {
      burger: Object,
      required: true 
    },
    
    data: function() {
  return {
    amountOrdered: 0
  }
  },
  methods: {
    increaseAmount: function() {
        this.amountOrdered += 1;
    this.addBurger();
    },
    decreaseAmount: function() {
      if (this.amountOrdered > 0) {
    this.amountOrdered-= 1; 
    this.addBurger(); 
    }
  },
  addBurger: function () {
  this.$emit('orderedBurger', { name:   this.burger.productName, 
                                amount: this.amountOrdered});
  },
  }
  }
  



  
  </script>
  
  <!-- Add "scoped" attribute to limit CSS to this component only -->
 <style scoped>
 
 </style>





  