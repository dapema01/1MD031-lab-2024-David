<template>
  <div class="burger">
    <section id="titleSec">
    <h2>{{ burger.name }}</h2>
    </section>
    <img :src="burger.imageUrl" :alt="burger.name" />
    <section id="infSec">
    <p>KCal:{{ burger.kCal }}</p>
    <p v-if="burger.gluten && burger.lactose">Contains gluten and lactose</p>
    <p v-else-if="burger.gluten">Contains gluten</p>
    <p v-else-if="burger.lactose">Contains lactose</p>
    <p v-else>No gluten or lactose</p>
    <p>Amount selected: {{ amountOrdered }}</p>
    </section>
    <div class="button-group">
      <button @click="amountOrdered++">Add to order</button>
      <button @click="amountOrdered--">Remove from order</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger:{
      type: Object,
      required: true,
    },
  },
  
  data: function(){
      return {
        amountOrdered: 0
      };
    },
    watch: {
      amountOrdered(value) {
        if (value < 0) this.amountOrdered = 0;
        this.$emit("update-order", { burger: this.burger, amount: this.amountOrdered});
      }
    }
  
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.burger {
  border-radius: 10px;
  padding: 15px;
  text-align: center;
  background-color: rgb(254, 202, 150);
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  width: 80%;
  height: fit-content;
  }
.burger img {
  max-width: 100%;
  border-radius: 5px;
  margin-bottom: 10px;
  align-items: center;
}

#titleSec {
  height: 20%;
}

.burger h2 {
  font-size: 1.5rem;
  margin-bottom: 10px;
  padding: 10px;
}

.burger p {
  font-size: 1rem;
  margin: 5px 0;
}
#infSec {
  height: 20%;
}

.button-group {
  display: flex;
  justify-content: center;
  gap: 10px; 
  margin-top: 10px;
}

.button-group button {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  background-color: #f0ebb3;
  color: rgba(0, 0, 0);
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.3s ease, transform 0.2s ease;
}

.button-group button:hover {
  
  transform: scale(1.05); 
}

.button-group button:active {
  background-color: #858366; 
  transform: scale(0.95); 
}
</style>