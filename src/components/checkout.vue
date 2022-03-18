<template>
<div> 
<!-- checkout page code -->
        
            <button v-on:click='backButton'>
                <span  class="fas fa-arrow-left"></span> Go Back
            </button>
 
            <!-- Basket code to diplay products in basket-->
            <h2> Your Basket </h2>
            <div class="contain">
                <div class="prod_box" v-for="(item, index) in cart" :key="index">
                    <p v-text="item.lessons.subject"> </p>
                    <img v-bind:src="item.lessons.image" height="50">
                    <p v-text="item.lessons.location"> </p>
                    <p>Price: {{item.lessons.price}}</p>
                    <button v-on:click="deleteFromCart(item)">
                    remove
                </button>
                </div>
            </div>

            <!--diplay checkout inputs for user-->
        <div id="checkout">
                <h2> Checkout</h2>

                <form action="https://gurpreet122p.github.io/Coursework1/" method="GET" onchange="return checkData()" name="signup">
                    <br /><br />Name:
                    <input name="NAME" type="text" id="NAME" v-model="OrderName" />
                    <br /><br /> Phone Number:
                    <input name="PHONE" type="text" id="PHONE" v-model="OrderPhone" />
                    <input @click="submitForm()" v-bind:disabled='formIsNotCorrect' id="Button" type="submit" value="Place Order">
                </form>
            </div> 
<script src="https://coursework2.herokuapp.com/checkoutScript.js" type="application/javascript"> </script>
</div>        

</template>

<script> 
export default {
name: 'checkout',
props: ['cart'],
data() {
   
    return {
     
       
            OrderName: '',
            OrderPhone: '',
               formIsNotCorrect: true
    }   
},
methods : {
    deleteFromCart (item){
            this.$emit('deleteFromCart', item)
    },
    backButton(){
             this.$emit('showCheckout')
    },
    submitForm(){
             this.$emit('submitForm')
    }
}
}
</script>