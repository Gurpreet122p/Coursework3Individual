<template>
       <div class="contain">
                <div class="prod_box" v-for="(lesson, index ) in lessons" :key=" 'A' + index">
                    
                    <!-- lesson information -->
                    <h2 v-text="lesson.subject"> </h2>
                    <img v-bind:src="lesson.image" height="125">
                    <p v-text="lesson.location"> </p>
                    <p>Price:£{{lesson.price}}</p>
                    <p>Available:{{lesson.availableInventory}}</p>
                    <!-- Font Awesome generated icons -->
                    <div>
                        <span v-for='n in lesson.rating' :key="n+index"><i class="fas fa-star"></i></span>
                        <span v-for='n in 5-lesson.rating' :key="n+index+index"><i class="far fa-star"></i></span>

                    </div>
                    <!-- Button for adding lesson to cart -->
                    <button v-on:click='addToCart(lesson)' v-if='lesson.availableInventory'>
                    Add to cart
                    </button>
                    <!-- Disable Button for adding lesson to cart when required-->
                    <button disabled='disabled' v-else>
                        Add to cart
                    </button>
                    <span v-if='lesson.availableInventory < 1'>
                        All out!
                    </span>
                    <span v-else-if="lesson.availableInventory  < 5 ">
                        Only {{lesson.availableInventory }} left!
                    </span>
                    <span v-else>Buy Now</span>
                </div>
            </div>
</template>

<script> 
export default {
name: 'productlist',
props: ['lessons'],
data() {
   
    return {
     
    }   
},
  methods:{
     addToCart: function(lessons) {
                function ifCanAdd(item) {
                    if (item.id == lessons.id) {
                        lessons.availableInventory -= 1;
                        return item;
                    }
                }
                this.lessons.find(ifCanAdd);
                
    this.$emit('addProduct', lessons)
            }
  }
  
}
</script>