<template>
    <main>
        
            <br>
            <form>
                <input type="radio" name="sort" value="subject" v-model="sortAttribute">
                <label for="subject">Subject</label><br>
                <input type="radio" name="sort" value="location" v-model="sortAttribute">
                <label for="location">Location</label><br>
                <input type="radio" name="sort" value="price" v-model="sortAttribute">
                <label for="price">Price</label><br>

                <br>

                <input type="radio" id="asc" name="order" value="asc" v-model="sortOrder">
                <label for="huey">Ascending</label><br>
                <input type="radio" id="desc" name="order" value="desc" v-model="sortOrder">
                <label for="huey">Descending</label><br>
            </form>
        
        
                <div v-bind:key="lesson._id" v-for="lesson in sortedLessons">
                  <div class="container">
                  <div class="item">
                    <img v-bind:src="lesson.image" width="200px" height="150px"> 
                    <h1>{{lesson.subject}}</h1>
                    <p>location: {{lesson.location}}</p>
                    <p>Price: £{{lesson.price}}</p>
                    <p>Spaces: {{lesson.spaces}}</p>
                    <button @click='addLesson(lesson)' :disabled='!countSpace(lesson)'>Add Lesson</button>
                    </div>
                    </div>
                </div>
            
        
    </main>
</template>

<script> 

export default {
    name: "LessonPage",
    props: ['lesson'],
    data() {
        return {
            searchLetter: '',
            sortedLessons: [],
            sortAttribute: 'subject',
            sortOrder: 'asc'
        };
    },
    methods: {
        addLesson(lesson){
            this.$emit('addItem',lesson)
        },
        countSpace(lesson){
            return lesson.spaces != 0
        },
        sorting(){
            let data = JSON.parse(JSON.stringify(this.lesson))

            if(this.searchLetter != ''){ //checks if the user has entered search information.
            //parsing data prevents mutation of original data by creating copy
            let searchData = JSON.parse(JSON.stringify(this.searchLessons))
            console.log(searchData);
              
              for (let i = 0; i < searchData.length; i++) {
                for(let y = 0; y < this.lesson.length; y++){
                  if(searchData[i]._id === this.lesson[y]._id){
                    searchData[i].spaces = this.lesson[y].spaces
                    console.log(this.lesson[i].spaces);
                  }
                }
              }
              data = searchData;
            }
            
            //sort the array based on the attribute selected
            if(this.sortAttribute == "subject"){
              data.sort(comparetopic);
            } else if(this.sortAttribute == "location"){
              data.sort(comparelocation);
            } else if(this.sortAttribute == "price"){
              data.sort(comparePrice); 
            } else if(this.sortAttribute == "availability"){
              data.sort(compareSpaces); 
            } else {
              data.reverse();
            }

            if(this.sortOrder == "asc"){
              this.sortedLessons = data
            } else {
              this.sortedLessons = data.reverse(); //reverse the array if the option to sort descending is selected
            }

            function comparetopic(a, b){
              let x = a.subject;
              let y = b.subject;
              if (x < y) {return -1;}
              if (x > y) {return 1;}
              return 0;
            }

            function comparelocation(a, b){
              let x = a.location;
              let y = b.location;
              if (x < y) {return -1;}
              if (x > y) {return 1;}
              return 0;
            }

            function comparePrice(a, b,){
              if(a.price > b.price) return 1;
              if(a.price < b.price) return -1;
              return 0;
            }

            function compareSpaces(a, b,){
              if(a.spaces > b.spaces) return 1;
              if(a.spaces < b.spaces) return -1;
              return 0;
            }
        }
    },

    //sort lessons as soon as user makes changes to data
    watch: {
        searchLessons: {
            handler(){
                this.sorting()
            }
        },
        lesson: {
            handler(){
                this.sorting()
            }
        },
        sortOrder: {
            handler(){
                this.sorting()
            }
        },
        sortAttribute: {
            handler(){
                this.sorting()
            }
        }
    },
    //sort lessons as soon as page loads
    created: function(){
        this.sorting()
    }
}
</script>
