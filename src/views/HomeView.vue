<template>

  <body>
    <header>
        <h1>Burgers Online</h1>
    </header>
<main>
    <section id="Intro">
        <div id="Headline">
            <h1>Welcome to BurgerOnline</h1>
            <img src="/img/Pubar-London-1200x630px.jpg">
        </div>

    </section>
    <section id="tabSec">
      <h2>Menu</h2>
        <div class="burger-grid">
            <Burger v-for="burger in burgers" :key="burger.name" :burger="burger" @update-order="updateOrder"/>
            
        </div>
      </section>
    <section id="contact">
        <form>
            <p>
                <label for="fullname">Full Name</label><br>
                <input type="text" id="fullname" name="fn" v-model="formData.fullname" required="required" placeholder="Full name">
                
      
            </p>
            <p>
                <label for="email">Email</label><br>
                <input type="email" id="email" name="em" v-model="formData.email" required="required" placeholder="E-mail address">
            </p>    
            <p>
                <label for="payment">Payment Method</label><br>
                <select id="paymetn" name="pm" v-model="formData.payment">
                    <option>Visa</option>
                    <option>Mastercard</option>
                    <option>Klarna</option>
                    <option selected="selected">Amex</option>
                </select>
            </p>      
            
            <p>
                    <label for="gender">Select your gender</label><br>
                    <input type="radio" id="male" name="gender" value="Male" v-model="formData.gender">
                    <label for="male">Male</label><br>
                    
                    <input type="radio" id="female" name="gender" value="Female" v-model="formData.gender">
                    <label for="female">Female</label><br>
                    
                    <input type="radio" id="no-gender" name="gender" value="No-gender" v-model="formData.gender">
                    <label for="no-gender">Do not wish to provide</label>
            </p>    
        </form>

        <div id="map-container">
            <div id="map" v-on:click="setLocation"><!-- v-on:click="addOrder" -->
                <div v-if="location.x !== 0 && location.y !== 0" class="dot"
                    :style="{ left: location.x + 'px', top: location.y + 'px' }">
                    T
                </div>
            </div>
        </div>
            <button type="submit" @click="handleSubmit">
                <img src="/img/png-clipart-submit-button-illustration-submit-button-icons-logos-emojis-submit-thumbnail.png">
            </button>
        
    </section>
</main>
    <footer>
        <br>
        <hr>
        &copy; 2024 BurgerOnline
    </footer>
  </body>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'


const socket = io("localhost:3000");

// const burgers = [
//   new MenuItem("The Fire Burger", "/public/img/andys_burgers_original_cheeseburger_10okt23_insta.jpg", 500, true, true),
//   new MenuItem("Cheeseburger", "/public/img/download.webp", 600, true, true),
//   new MenuItem("Halloumi Burger", "/public/img/images.jpeg", 450, false, false)
// ];

// function MenuItem(name, imageUrl, kCal, containsGluten, containsLactose) {
//   this.name = name;
//   this.imageUrl = imageUrl;
//   this.kCal = kCal;
//   this.containsGluten = containsGluten;
//   this.containsLactose = containsLactose;
// }



export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menu,
      orderedBurgers:{},
      location: { x: 0, y: 0 },
      formData: {
        fullname: "",
        email: "",
        payment: "",
        gender:""
      },
      dot: null,
    }
  },
  methods: {
    updateOrder({burger, amount})
    {
      if(amount === 0)
      {
        delete this.orderedBurgers[burger.name];
      }
      else
      {
        this.orderedBurgers[burger.name] = amount;
      }
      console.log(this.orderedBurgers);
    },

    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    handleSubmit: function () {

      if(!this.formData.fullname || !this.formData.email || !this.formData.payment || Object.keys(this.orderedBurgers).length === 0 || !this.location.x || !this.location.y){
        alert("Please fill in all fields");
        console.log("Order submited unsuccessfully");
        return;
      }
        alert("Order submitted successfully");
        socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { 
                                    x: this.location.x,
                                    y: this.location.y,
                                    name: this.formData.fullname,
                                    email: this.formData.email,
                                    payment: this.formData.payment,
                                    gender: this.formData.gender
                                },
                                orderItems: this.orderedBurgers,
                            });
        console.log("Order submited successfully");
    },
    setLocation: function (event){
    
        const mapRect = event.currentTarget.getBoundingClientRect();
        this.location.x = event.clientX - mapRect.left;
        this.location.y = event.clientY - mapRect.top;
    }
    }
};
</script>

<style >
  @import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');
body {
    font-size: 12pt;
    font-family: arial;
}

p {
    color: red;
}

h1 {
    font-family: 'Agbalumo';
    font-size: 36pt;
    
}
main, header, footer, nav ul {
    max-width: min(70rem, 80%);
    margin: 0 auto 0 auto;
}
main {
    background-color: bisque;
}

/* nav ul li {
    display: inline-block;
    background-color: grey;
    padding: 1em;
    margin: 1em;
} */

header {
    display: flex;
    background-image: url("../img/polacks.jpg");
    background-size: cover;
    overflow: hidden;
    width: 100%;
    height: 200px;
    opacity: 0.5;
    margin-bottom: 20px;
}

header h1 {
    width: 40rem;
    margin: 0 auto;
    text-align: center;
    padding: 30px;
}

#Intro h2, #Intro p {
    text-align: center;
}

nav ul {
    display: grid;
    grid-template-columns: repeat(auto-fill, 9.25em);
    gap: 1em;
    padding: 0;
}

nav li {
    display: block;
    background-color: grey;
    padding: 1em;
}

.Very-good {
    color: green;
}

.Master {
    color: green;
    font-weight: bold;
}
#Headline {
    position: relative;
    margin: 0 auto;
    padding: 20px 0;
    width: 100%;
    height: 250px;
    display: flex;
    align-items: center;
    justify-content: center;
    text-align: center;
    overflow: hidden;
}

#Headline h1 {
    position: absolute;
    z-index: 1;
    font-size: 2.5rem;
    font-family: 'Agbalumo', sans-serif; 
    text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.7); 
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}

#Headline img {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    object-fit: cover;
    opacity: 0.3; 
}

#tabSec {
    border: 2px dashed red;
    margin: 20px;
}

#tabSec h2 {
  padding: 10px;
  text-align: center;
}


.burger-grid {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr; 
  grid-gap: 20px;
  padding: 20px;
  justify-items: center; 
}

.Ingridients {
    font-weight: bold;
}

#contact {
    margin: 20px;
    display: flex;
    flex-direction: column;
    background-color: black;
    color: white;
    justify-content: center;
    align-items: center;
    border: 2px dashed white;
    padding: 20px;
}


form p {
    color: white;
    padding: 10px 20px;
    max-height: 100%
}

#contact button {
    width: 200px;
    height: 200px;
    margin: 40px;
    padding: 0; 
    display: flex;
    justify-content: center;
    align-items: center;
    border: none; 
    background-color: transparent; 
    cursor: pointer;
}
#contact img {
    width: 100%; 
    height: 100%;
    object-fit: contain;
    border-radius: 5px;
    
}
#contact button:hover {
    transform: scale(1.1);
    transition: transform 0.2s;
}
#map-container {
  width: 960px; 
  height: 539px; 
  overflow: scroll; 
  border: 1px solid #ccc; 
  
}

#map {
    position: relative;
    width: 1920px;
    height: 1078px; 
    background: url("/img/polacks.jpg") no-repeat center center;
    background-size: cover;
    cursor: pointer;
}

.dot {
    position: absolute;
    background: black;
    color: white;
    border-radius: 50%;
    width: 20px;
    height: 20px;
    text-align: center;
    line-height: 20px;
    font-size: 14px;
    transform: translate(-50%, -50%);
}


@media (max-width: 1024px) {
    #tabSec {
        height: 1800px;
    }
    .burger-grid {
        grid-template-columns: 1fr;
        height: 500px;
    }
    .burger-grid img {
      
        height: 60%;
    }
}
</style>