<template>
  <div id="app">

        <!-- header -->
    <div class="jumbotron">

        <div class="container">
      <!-- Grid  -->
            <div class="row">

                <div class="col">
                    <h2 class="logo">Lesson</h2>
                  </div>
                  <button @click="deleteAllCaches" class="test-elem">
              <font-awesome-icon icon="fas fa-trash" />
              Delete All Caches
            </button>
            <button @click="unregisterAllServiceWorkers" class="test-elem">
              <span class="fab fa-uniregistry"></span>
              Unregister All ServiceWorkers
            </button>
                  <div class="col-md-6">
                    <form>
                        <input class="search_form" type="text" v-model="searchTxt" v-on:keyup="searchLessons" placeholder="Search Courses" >
                    </form>
                  </div>
                  <div class="col"><!-- Disable and Enable the cart depending on whether or not it is empty --><button class="cart-button btn" v-bind:disabled="(cartItems.length <= 0)" v-on:click="updateShowProduct"><i class="fa fa-shopping-cart"></i> Cart {{cartCount()}} </button></div>
            </div>

        </div>

    </div>
        <!-- end of header -->
  <div class="container-fluid nav_view p-4">
    <h3 class="text-center">
        Home | Store
    </h3>
   </div>


        <section v-if="showProduct" id="product">
            <Lessons :lessons="lessons" v-on:addLesson="addLessonToCart"></Lessons>
        </section>

        <section v-else id="check-out">
            <Checkout :cartItems="cartItems" v-on:removeLesson="removeLessonFromCart" v-on:checkOut="checkOut"></Checkout>
        </section>
  </div>
</template>

<script>
import Lessons from './components/Lessons.vue'
import Checkout from './components/Checkout.vue'
export default {
  name: 'App',
  components: {
    Lessons,
    Checkout
  },
  data() {
    return {
       lessons: [],
      
        cartItems: [
        ],
        showProduct: true,
        searchTxt: '',
        
        
    }
  },
  methods: {
    addLessonToCart: function (lessonID, lesson) {
          
            const lessonIndex = this.lessons.findIndex(lesson => lesson._id === lessonID);
            if (lessonIndex != -1) {
                console.log("heeeree");
                this.lessons[lessonIndex].availablespace -= 1;       
            }
             this.addToCart(lessonID, lesson);
        },
        
        updateShowProduct: function () {
            this.showProduct = !this.showProduct;
        },
        addToCart: function (lessonID, lesson) {
          console.log("here");
            const itemIndex = this.cartItems.findIndex(item => item.lessonID === lessonID);
            if (itemIndex != -1) {
                this.cartItems[itemIndex].availablespace += 1;
            } else {
                this.cartItems.push({ lessonID: lessonID, availablespace: 1, lesson: lesson });
            }
        },
        removeLessonFromCart: function (index) {
            this.cartItems[index].availablespace = this.cartItems[index].availablespace - 1;
            const lessonIndex = this.lessons.findIndex(lesson => lesson._id === this.cartItems[index].lessonID);
            if (lessonIndex != -1) {
                this.lessons[lessonIndex].availablespace += 1;
            }
            if (this.cartItems[index].availablespace == 0) {
                this.cartItems.splice(index, 1);
            }
            if(this.cartItems.length == 0){
              this.showProduct = true;
            }
            console.log(this.cartItems);
        },
        cartCount: function(){
            let cartQuantity = 0;
            for (let index = 0; index < this.cartItems.length; index++) {
                cartQuantity += this.cartItems[index].availablespace;
                
            }
            return cartQuantity;
        },
        searchLessons: function () {
            
            fetch("https://cw2-backend.onrender.com/products/" + this.searchTxt).then(
            (response) => {
                response.json().then(
                    (data) => {
                        console.log(data);
                        this.lessons = data;
                        console.log(this.lessons);
                    });
            })
        },
        checkOut: function (checkOutName, checkOutNumber) {
            fetch('https://cw2-backend.onrender.com/collection/orders', {
                method: 'POST', // set the HTTP method as 'POST'
                headers: {
                    'Content-Type': 'application/json', // set the data type as JSON
                },
                body: JSON.stringify({ name: checkOutName, phone: checkOutNumber, lessons: this.cartItems }), // need to stringify the JSON object
            })
                .then(response => response.json())
                .then(responseJSON => {
                    console.log('Success:', responseJSON);
                });
            for (let index = 0; index < this.cartItems.length; index++) {
                const lesson = this.cartItems[index].lesson;
                fetch(`https://cw2-backend.onrender.com/collection/proucts/${lesson._id}`, {
                    method: 'PUT', // set the HTTP method as 'POST'
                    headers: {
                        'Content-Type': 'application/json', // set the data type as JSON
                    },
                    body: JSON.stringify({ availablespace: lesson.availablespace}), // need to stringify the JSON object
                })
                    .then(response => response.json())
                    .then(responseJSON => {
                        console.log('Success:', responseJSON);
                    });
            }
            // alert("Check out successful\nYour order has been submitted");
            this.showProduct = true;
            this.cartItems = [];
            this.$swal({
                title: "Check out successful",
                text: "Your order has been submitted",
                icon: "success",
            });
            
        },
        fetchLesson: function (_id) {
            const lessonIndex = this.lessons.findIndex(lesson => lesson._id === _id);
            if (lessonIndex == -1) {
                return;
            }
            return this.lessons[lessonIndex];
        },
    unregisterAllServiceWorkers() {
      navigator.serviceWorker.getRegistrations().then(function (registrations) {
        for (let registration of registrations) {
          registration.unregister();
        }
      });
      console.log("ServiceWorkers Unregistered");
    },
    deleteAllCaches() {
      caches.keys().then(function (names) {
        for (let name of names) caches.delete(name);
      });
      console.log("All Caches Deleted");
    },
       
    },
    
    created: function () {
        if("serviceWorker" in navigator){
            navigator.serviceWorker.register("../service-worker.js")
        }
        fetch("https://cw2-backend.onrender.com/collection/products").then(
             (response) => {
                response.json().then(
                     (data) => {
                        console.log(data);
                        this.lessons = data;
                        console.log(this.lessons + "Fetched");
                    });
            })
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
  margin-top: 60px;
}
</style>
