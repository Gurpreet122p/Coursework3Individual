
<template> 

   <div id="app">
        <!--  <script src='https://coursework2.herokuapp.com/service-worker.js'  type="application/javascript" > </script>    --> 

   <head>
     <h1>{{sitename}}</h1>

     <title>Courswork 3 Gur Preet Singh Kaur</title>

    <!-- Font-Awesome library -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.1/css/all.min.css">
<!-- Service Worker for the app -->
    
    <!-- Manifest -->
    <link rel="manifest" src="https://coursework2.herokuapp.com/manifest.webmanifest"  >

   </head>
       <header>
                <button v-on:click='showCheckout' v-if="this.cart.length">
                {{this.cart.length}}
                <span class="fas fa-shopping-cart"></span> Checkout
            </button>
       </header>
<main>
        <!-- Sorting Options -->
            <div id="sort">
                <p>Sort by</p>
                <div v-for="(selectedValue, key) in sorting" :key=" 'A' + key">
                
                    <input type="radio" id="key" name="key" value="selectedValue" v-on:click='sortBy(selectedValue)'>
                    <label for="key"> {{key}}</label><br>
                </div>

                <p>
                    <strong>Order:</strong>
                    <select v-model="orderAscending">
                    <option disabled value="">Order By</option>
                    <option v-for="(selectedValue, key) in userOrder" v-on:change='ss' v-bind:value="selectedValue"  :key=" 'A' + key">
                        {{selectedValue}}
                        </option>
                </select>
                </p>
            </div>

            <!-- Searching Options -->
            <div id="search">
                <input placeholder="Search..." type="text" v-model="search">  {{filteredLessons}}
            </div>
 
  <div v-if="currentView"> 
      <lesson  :lessons="lessons"  @addProduct='addToCart'/>
  </div>
 
  <div v-else > 
    <checkout  @showCheckout='showCheckout' :cart="cart" @deleteFromCart='deleteFromCart' @submitForm='submitForm' />
  </div>
   
</main>
 

  </div>
</template>

<script>
/* eslint-disable */
import checkout from './components/checkout.vue'
import lesson from './components/lesson.vue'


export default {
  name: 'App',
  components: { checkout, lesson },
  data(){
    return{
      sitename: 'BookingSystem',
      cart: [],
      currentView: true,
      lessons:[],
     orderAscending: '',
     search: '',
     sorting: {Price: 'price',
                Subject: 'subject',
                Location: 'location',
                Availability: 'availableInventory'},
    userOrder: {
        a: 'Ascending',
        b: 'Descending'
            },
    }
  },
  methods:{

   showCheckout() {
                this.currentView = this.currentView ? false : true;
            },
    submitForm() {
                for (let s = 0; s < this.lessons.length; s++) {
                    let lessonID = this.lessons[s].id


                    let spaces = this.cartCount(lessonID)
                    if (spaces > 0) {
                      
                        this.cartLessonsID.push({
                            lessonID

                        })
                        this.cartLessonsCounter.push({
                            spaces
                        })
                    }

                }



                fetch('https://coursework2.herokuapp.com/collection/Order', {
                    method: 'POST',
                    headers: {
                        'Accept': 'application/json',
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({
                        "LessonID": this.cartLessonsID,
                        "Name": this.OrderName,
                        "Phone": this.OrderPhone,
                        "Spaces": this.cartLessonsCounter

                    }),
                })


                for (let i = 0; i < this.cartLessonsID.length; i++) { //Loop for every lesson that is sent to the database

                    for (let s = 0; s < this.lessons.length; s++) { //Loop for every lesson available
                        let lessonID = this.lessons[s].id



                        if (lessonID === this.cartLessonsID[i].lessonID) {
                            var availableSpace = this.lessons[s].availableInventory // - this.cartLessonsCounter[i].counter

                            var tmpLessonID = this.cartLessonsID[i].lessonID
                            console.log(tmpLessonID + 'This is the TEMPORARY LESSONS ID ')

                            fetch('https://coursework2.herokuapp.com/update/Lessons/' + lessonID, {
                                method: 'PUT',
                                headers: {
                                    'Accept': 'application/json',
                                    'Content-Type': 'application/json'
                                },
                                body: JSON.stringify({
                                    "availableInventory": availableSpace
                                }),
                            })

                        }

                    }


                }

                alert('Order Submitted Successfully!')

                document.location.reload(true)

            },
            addToCart(lessons){

              let tmpLessonID = lessons.id           

                this.cart.push({
                    cartId: (this.cart.length + 1),
                    lessons,
                    tmpLessonID
                })
            },
            deleteFromCart: function(lesson) {
                this.cart = this.cart.filter(item => item.cartId != lesson.cartId)

                function ifCan(item) {
                    if (item.id == lesson.lessons.id) {
                        console.log("adding " + item.id)
                        item.availableInventory += 1
                    }
                }
                             
                this.lessons.find(ifCan)
       }
  }, 
  computed: {
              ss() {
                if (this.orderAscending === "Ascending") {
           
                     this.orderAscending = "Ascending"
           
                     return this.lessons.reverse();

                } else if (this.orderAscending === "Descending") {
                   
                    this.orderAscending = "Descending"
                  
                    return this.lessons.reverse();

                } else {
                
                    return this.lessons;
                }


            },
    filteredLessons() {



                var localApp = this;


 

                if (!this.search) {
                   
                   
                    fetch('https://coursework2.herokuapp.com/collection/Lessons', {
                        method: 'GET'
                    })
                        .then(function(response) {
                            if (response.ok) {
                                console.log('Successfully fetched the Data')
                            } else {
                                console.log('Error fetching data')
                            }

                            return response.json();
                        })
                        .then(function(json) {
                            // use the json
                            console.log(json)
                            localApp.lessons = json;

                        }).catch(function(error) {
                        //Handle error
                        console.log(error);
                    });
                } else {

           
                    fetch('https://coursework2.herokuapp.com/Search/Lessons/?search=' + this.search + '', {
                        method: 'GET'
                    })
                        .then(function(response) {
                            if (response.ok) {
                                console.log('Success')
                            } else {
                                console.log('Error fetching data')
                            }

                            return response.json();
                        })
                        .then(function(json) {

                            console.log(json)
                            localApp.lessons = json;

                        })

                }


 
 
  
 }

       
  },
        created: function() { //Vue lifecycle hook, called when data is loaded.
/*
                  var localApp = this;


            
                    // IF There is nothing in the search bar then retrieve all lessons otherwise perform search query
                    fetch('https://coursework2.herokuapp.com/collection/Lessons', {
                            method: 'GET'
                        })
                        .then(function(response) {
                            if (response.ok) {
                                console.log('Successfully fetched the Data')
                            } else {
                                console.log('Error fetching data')
                            }

                            return response.json();
                        })
                        .then(function(json) {
                            // use the json
                            console.log(json)
                            localApp.lessons = json;

                        }).catch(function(error) {
                            //Handle error
                            console.log(error);
                        });
                */

                this.filteredLessons;

        }
  
}
</script>
 
 <style> body {
    font-family: Verdana;
    font-size: 15px;
    line-height: 1.5;
    padding: 0.5%;
    margin: 0.5%;
    background-color: #f4f4f4;
}

#app {
    width: 80%;
}

.prod_box {
    float: left;
    display: inline;
    padding: 1%;
    margin: 1%;
}

header {
    position: absolute;
    right: 50%;
}

.contain {
    width: 100%;
    float: left;
    display: inline;
    border-top: 2px solid gray;
    border-bottom: 2px solid gray;
}

#checkout {
    display: block;
    width: 200px;
}

#search li {
    list-style-type: none;
    text-align: left;
    background-color: #f6f6f6;
    /* Grey background color */
    display: block;
    /* Make it into a block element to fill the whole list */
    border-left: 2px solid #000;
    width: 165px;
}

#search {
    margin-bottom: 1%;
}

#search ul {
    padding: 0px;
}

#search {
    padding: 0px;
    width: 18%;
    height: 10%;
}

#search ul :hover {
    background-color: rgba(238, 238, 238, 0.87);
    /* Add a hover effect to all links, except for headers */
}
</style>
