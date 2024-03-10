<template>
   <div>
        <h2 class="text-center mt-5 mb-5"></h2>
        <div class="container">
            <div class="row">
                <div class="col-md-2">
                <aside>
                    <h6>Sort By</h6>
                    <div class="custom-control custom-radio">
                      <input type="radio" id="subject" value="subject" name=""  v-model="attribute" class="custom-control-input">
                      <label class="custom-control-label" for="subject">Subject</label>
                    </div>
                    <div class="custom-control custom-radio">
                      <input type="radio" id="location" value="location" name=""  v-model="attribute" class="custom-control-input">
                      <label class="custom-control-label" for="location">Location</label>
                    </div>
                    <div class="custom-control custom-radio mb-1">
                      <input type="radio" id="price" value="price" name="" v-model="attribute" class="custom-control-input">
                      <label class="custom-control-label" for="price">Price</label>
                    </div>
                    <div class="custom-control custom-radio mb-1">
                      <input type="radio" id="availability" value="availability" name="" v-model="attribute" class="custom-control-input">
                      <label class="custom-control-label" for="availability">Availability</label>
                    </div>

                    <!-- order by ascending or descedning order -->
                    <h6 class="mt-4">Order By</h6>
                    <div class="custom-control custom-radio mb-1">
                      <input type="radio" id="ascending" value="ascending" name="" class="custom-control-input">
                      <label class="custom-control-label" for="ascending">Ascending</label>
                    </div>
                    <div class="custom-control custom-radio mb-1">
                      <input type="radio" id="descending"  value="descending" name="" class="custom-control-input">
                      <label class="custom-control-label" for="descending">Descending</label>
                    </div>
                    <!-- end of order by ascending or descending order -->

                </aside>
                </div>
                <!-- UI to display lesson items -->
                <div class="col-md-10 row">

                 <div v-if="(lessons.length > 0)" class="row grid-1">

                    <div  v-for="(lesson) in sortedLessons" :key="lesson.id" class="col-md-4">
                    <div class="card mb-4">
                      <img class="card-img" v-bind:src="`https://cw2-backend.onrender.com/${lesson.image}`" alt="Lesson Image">
                        <div class="card-body">
                            <h6>Subject: {{lesson.subject}}</h6>
                            <h6>Location: {{lesson.location}}</h6>
                            <h6>Available: {{lesson.availablespace}}</h6>
                            <h6>Price: £{{lesson.price}}</h6>
                              <h6>Rating: 
                                <span v-for="n in lesson.rating" :key="n.id">★</span>
                                <span v-for="n in 5 - lesson.rating" :key="n.id">☆</span>
                            </h6>
                            <button v-bind:disabled="(lesson.availablespace <= 0)" v-on:click="addLesson(lesson._id, lesson)" class="add_to_card-btn btn">Add to Cart</button>
                            <!-- End of disable cart -->
                        </div>
                    </div>
                </div>

                

                </div>

                <div v-else class="row grid-1">
                 <p style="margin: auto;">No result found</p>
                </div>
                <!-- end of UI to display lesson items -->
            </div>
            </div>
        </div>

</div>


    
</template>

<script>

export default {
    name: 'Lessons',
    props: ['lessons'],
    methods: {
        addLesson: function (lessonID, lesson) {
             this.$emit('addLesson', lessonID, lesson);
        },
    },
    data() {
        return {
           
        order: 'asc',
        attribute: 'subject',
        sort: {
            sortingMethod: "location",
            sortingOrder: "ascending",
        },
        sortAttributes: {
            Subject: 'subject',
            Location: 'location',
            Price: 'price',
            Spaces: 'spaces'
        },
        }
    },
    computed: {
        sortedLessons() {
            function compareSubjectAsc(a, b) {
                if (a.subject > b.subject) return 1;
                if (a.subject < b.subject) return -1;
                return 0;
            }
            function compareSubjectDesc(a, b) {
                if (a.subject > b.subject) return -1;
                if (a.subject < b.subject) return 1;
                return 0;
            }

            function compareLocationAsc(a, b) {
                if (a.location > b.location) return 1;
                if (a.location < b.location) return -1;
                return 0;
            }
            function compareLocationDesc(a, b) {
                if (a.location > b.location) return -1;
                if (a.location < b.location) return 1;
                return 0;
            }

            function comparePriceAsc(a, b) {
                if (a.price > b.price) return 1;
                if (a.price < b.price) return -1;
                return 0;
            }
            function comparePriceDesc(a, b) {
                if (a.price > b.price) return -1;
                if (a.price < b.price) return 1;
                return 0;
            }

            function compareSpacesAsc(a, b) {
                if (a.space > b.space) return 1;
                if (a.space < b.space) return -1;
                return 0;
            }
            function compareSpacesDesc(a, b) {
                if (a.space > b.space) return -1;
                if (a.space < b.space) return 1;
                return 0;
            }

            if (this.order == "asc") {
                if (this.attribute == "subject") {
                  // eslint-disable-next-line vue/no-side-effects-in-computed-properties
                    return this.lessons.sort(compareSubjectAsc);
                } else if (this.attribute == "location") {
                  // eslint-disable-next-line vue/no-side-effects-in-computed-properties
                    return this.lessons.sort(compareLocationAsc);
                } else if (this.attribute == "price") {
                  // eslint-disable-next-line vue/no-side-effects-in-computed-properties
                    return this.lessons.sort(comparePriceAsc);
                } else {
                  // eslint-disable-next-line vue/no-side-effects-in-computed-properties
                    return this.lessons.sort(compareSpacesAsc);
                }

            } else {
                if (this.attribute == "subject") {
                  // eslint-disable-next-line vue/no-side-effects-in-computed-properties
                    return this.lessons.sort(compareSubjectDesc);
                } else if (this.attribute == "location") {
                  // eslint-disable-next-line vue/no-side-effects-in-computed-properties
                    return this.lessons.sort(compareLocationDesc);
                } else if (this.attribute == "price") {
                  // eslint-disable-next-line vue/no-side-effects-in-computed-properties
                    return this.lessons.sort(comparePriceDesc);
                } else {
                  // eslint-disable-next-line vue/no-side-effects-in-computed-properties
                    return this.lessons.sort(compareSpacesDesc);
                }
            }
        }
    },
}
</script>
