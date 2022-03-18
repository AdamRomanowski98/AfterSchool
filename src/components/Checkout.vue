<template>
<main>
<div>
    <div id = "controls-checkout">
        <p>Please enter your details</p>
        <label for="fname">Name</label><br>
        <input v-model="firstName"><br>
        <label for="avail">Phone Number</label><br>
        <input v-model="phoneNo"><br>
        <button @click='placeOrder()'>Place Order</button>
    </div>

        <div v-bind:key="lesson.key" v-for="lesson in getLessons()"> 
            <div class="container">
            <div class="item">
            <img v-bind:src="lesson.image" width="200px" height="150px"> 
            <h1>{{lesson.topic}}</h1>
            <p>location: {{lesson.location}}</p>
            <p>Price: £{{lesson.price}}</p>
            <button @click='removeLesson(lesson._id)'><span v-bind:class="lesson.icon"></span> Remove Lesson</button>
            </div>
            </div>
        </div>
    

</div>
</main>
</template>

<script>
export default {
    name: "CheckoutPage",
    props: ['cart','lesson'],
    data() {
        return {
            firstName: '',
            phoneNo: ''
        };
    },
    methods: {
        removeLesson(lessonid){
            this.$emit('removeItem',lessonid)
        },
        getLessons(){
            //compares IDs in the cart against lessons in array of lessons
            let lessonDetails = []
            for (let id of this.cart){
              for (let _lesson of this.lesson){
                if(id === _lesson._id){
                  lessonDetails.push(_lesson)
                }
              }
            }
            return lessonDetails;
        },
        placeOrder(){
            let spaces = [];
            let ids = [];

            let lessonData = JSON.parse(JSON.stringify(this.lesson))

            //stack the same lesson and increment count of it within one order
            this.cart.forEach(function(cartItem){
                lessonData.forEach(function(lessonObj){
                if(cartItem === lessonObj._id && ids.includes(cartItem) == false ){
                    let temp = lessonObj.topic + "-" + lessonObj.location
                    spaces.push({Lesson: temp,Ordered: 0})
                    ids.push(cartItem);
                }
                });
            });

            this.cart.forEach(function(cartItem){
                lessonData.forEach(function(lessonObj){
                if(cartItem === lessonObj._id){
                    let temp = lessonObj.topic + "-" + lessonObj.location
                    for(let i = 0; i < spaces.length; i++){
                        if(temp == spaces[i].Lesson){
                            spaces[i].Ordered +=1;
                        }
                    }
                }
                });
            });

            console.log(spaces);
            console.log(ids);

            const order = {name: this.firstName, phoneNumber: this.phoneNo, lessonID: ids, orderedSpaces: spaces};

            fetch('https://afterschoolclub-adamromanowski.herokuapp.com/collection/orders', { 
                method: 'POST',
                headers: {
                'Content-Type': 'application/json',
                },
                body: JSON.stringify(order),
            })
            .then(response => response.json())
            .then(responseJSON => {
                console.log('Success:', responseJSON);
            });

            for (let i = 0; i < this.cart.length; i++) {
                for(let y = 0; y < this.lesson.length; y++){
                if(this.cart[i] === this.lesson[y]._id){
                    let updateSpaces = { "spaces": (this.lesson[y].spaces) };
                    fetch("https://afterschoolclub-adamromanowski.herokuapp.com/collection/lessons" + this.cart[i], {
                    method: "PUT", 
                    headers: {
                        "Content-Type": "application/json",
                    },
                    body: JSON.stringify(updateSpaces), 
                    });
                }
                }
            }
            this.$emit('emptyCart')

            fetch('https://afterschoolclub-adamromanowski.herokuapp.com/collection/lessons')
            .then(response => response.json())
            .then(data => (this.searchLessons = data))
        },
    },
}
</script>
