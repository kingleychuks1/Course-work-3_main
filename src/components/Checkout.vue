<template>
     <div>
        <div class="container mt-5 mb-5">
            <div class="row">
                <div v-for="(cartItem, index) in cartItems" :key="cartItem.lessonID" class="col-md-3 mb-4">
                    <div class="card">
                      <img class="card-img" v-bind:src="`https://cw2-backend.onrender.com/${cartItem.lesson.image}`" alt="Lesson Image">
                        <div class="card-body">
                          <h6>Subject: {{ cartItem.lesson.title }}</h6>
                          <h6>Location: Location: {{ cartItem.lesson.location }}</h6>
                          <h6>Price: £{{ cartItem.lesson.price }}</h6>
                            <button  v-on:click="removeLesson(index)" class="btn add_to_card-btn">Remove from Cart</button>
                        </div>
                    </div>
                </div>
            </div>
            <!-- Start of checkout UI -->
            <h2 class="text-center mt-5 mb-5">Checkout</h2>
            <form class="mb-5" action="#">
              <div class="form-row">
                <div class="col">
                  <input type="text" class="form-control" v-on:keyup="validateRegexCheckOut" v-model="checkOutName" placeholder="Enter Name" required>
                </div>
                <div class="col">
                  <input type="text" class="form-control" v-on:keyup="validateRegexCheckOut"  v-model="checkOutNumber" placeholder="Phone Number" required>
                </div>
                <input style="margin-bottom: 50px;" v-bind:disabled="cannotCheckOut" v-on:click="checkOut(checkOutName, checkOutNumber)" class="btn add_to_card-btn" type="submit" value="Place Order">
                 
              </div>
            </form>
            <!-- End of checkout UI -->
            </div>
        </div>
</template>

<script>
export default {
    name: 'Checkout',
    props: ['cartItems'],
    data() {
        return {
            checkOutName: '',
        checkOutNumber: '',
        cannotCheckOut: true
        }
    },
    methods: {
        removeLesson: function(index){
             this.$emit('removeLesson', index);
        },
         validateRegexCheckOut: function () {
            var nameRegexPattern = /^[a-zA-Z ]*$/;
            var numberRegexPattern = /^\d+$/;
            if (this.checkOutName.match(nameRegexPattern) && this.checkOutNumber.match(numberRegexPattern)) {
                this.cannotCheckOut = false;
            } else {
                this.cannotCheckOut = true;
            }
        },
        checkOut: function (checkOutName, checkOutNumber) {   
             this.$emit('checkOut',checkOutName, checkOutNumber);
        }
    }
}
</script>