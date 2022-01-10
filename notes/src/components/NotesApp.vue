<template>
    <div class="container">
    <form @submit.prevent="handleSubmit" >
      <!-- @click.prevent="handleSubmit" -->

    <div class="leftbox">

    <label> Title: </label> 
    <input type="text" v-model.trim="title" > 
    <span class="errorMsg" v-if="sametitle == true"><p>Sorry, can't add the same title. </p></span>
    <span class="errorMsg" v-else-if="checkTitle" > Title Should be At least 2 characters !</span>
    <span span v-else>  </span>
    <br>

    <label> Author: </label>
    <input type="text" v-model="author" >
    <span class="errorMsg" v-if="checkAuthor">Author Should be At least 2 characters !</span>

    <br>
    <label> Year of Publication:</label>
    <input type="number" v-model ="year" >
    
    <span class="errorMsg" v-if="year == null">  </span>
    <!-- <span class="errorMsg" v-else-if="(typeof(year)== String) "> Year should be a Number  </span> -->
    <span class="errorMsg" v-else-if="checkYear"> Year should be Format: YYYY (e.g. earlier than 2023) </span>
    <span class="errorMsg" v-else>  </span> 
    
    <div>
      <input type="checkbox" v-model="read" >
        <label> Read </label>
    </div>
    
    
    <br/>
    <!-- disable the 'Add New Note' if nothing has typed in.  -->
    <button @click="addNote(title,author,year)" :disabled="isDisable"> Add One Note </button> 
    <button @click="allNotes" > Show All Notes </button>
    <button type="reset" @click="clearInputs"> Clear </button>
    
    

    <div class="sill" v-if="newNote" >
    <div v-for="item in displayNewNote.slice(0,3)" :key="item.id" >
       <span > {{ item }} </span>
    </div>
       <span class="material-icons" v-if="flag==='YES'" >flag</span>
       <!-- <span class="material-icons" v-else>outlined_flag</span> -->
    </div>

    <div class="search" >
    <input type="searchtext" v-model="searchVal" >
    <button @click="search(searchVal)" id="searchbutton"> Search </button>
    <span v-if="showSearch === 'NO'"><p> Sorry, the title does not exist! </p></span>
    <span v-else-if="showSearch ==='YES'">  </span>
    </div>
      </div>
     
    
    <div class="rightbox" v-if="isShow">
    <div class="rightbox-content" >
      <div v-if="showAll">
         <!-- box1 is to show all the result  -->
      <div class="box1" v-for="item in notes" :key="item.id" > 
        <span @click="deleteNote(item)" class="sill" > Click to delete:<br/> <span v-for ="name,index in noteItem" :key="index" class="showItem">
          <br/> {{ name }} {{item[index]}}; 
         </span>  </span> 
        <span class="material-icons" v-if="item[3]==='YES'" >flag</span>
        </div>
      </div>
      
      
      <div class="box2" v-if="showSearch ==='YES'" > 
        <span><button class="deleteSearch" type="button" @click="deleteNote(searchRes)" > Delete Result </button></span>
        <div v-for="item in displaySearch" :key="item.id" class="searchBox" > 
          <span>  {{ item }}</span>
        </div>

      </div>
      <!-- box 2 is to show all the notes; -->
  </div>
    </div>
      
   
  </form>
  </div>
  <!-- {{ title }} {{ author }} {{ year }} --> 
</template>

<script>

export default {
  data(){
    return {
    title: '',
    author: '',
    year: null,
    read: false,   //check if the book is read; 
    flag: '',
    sametitle: false,  //check if the title entered has existed. 
    newNote: [],
    noteItem: ["Title: ", "Author: ", "Year of Publication: ", "Read? "],
    notes: [], //this is to store all the notes added; 
    searchVal: '',   // get the whatever the user enter in the search bar; 
    searchRes: [],   // to store the result if the search matches; 
    showSearch: '',     // a string to store for the whole matched note in order to display;
    isShow: false,     // controls the right panel to show or not; 
    showAll: false,   // decides if all the notes stored in 'notes' to be shown or not. 
    }
  },
  
  methods: {
    addNote(title, author, year){
      title = title.toUpperCase();
      this.newNote = [ title,author,year];
      if(this.read){
        this.flag = 'YES';
      } else{
        this.flag = 'NO';
      };
      this.newNote.push(this.flag); //check if book's read;   
      
      this.notes.forEach(item => {
        if (item.includes(title)){
          this.sametitle = true;
          } else {
            this.sametitle = false;
          }
      });

      if(this.sametitle == false && title !== " " && title !== ''){
        this.notes.push(this.newNote);
        
      } else if(this.sametitle == true) {
        console.log('Sorry, same title');
        
      } else {
        console.log('No content entered ')
      }
  },
  
  deleteNote(note){
      console.log(note);
      this.notes = this.notes.filter((item) => note != item);
      this.showSearch = '';
    },
  
  allNotes(){
      this.isShow = !this.isShow;
      this.showAll = true;

      this.showSearch = '';
      // console.log('yes, should pop out');
  },

  clearInputs(){
    this.title = '',
    this.author = '',
    this.year = null,
    this.flag = '';
    this.newNote = [];
    this.sametitle = false;
    // this.searchVal = '';
  },
  search(val){
    var value; 
    val = val.toUpperCase();
     this.notes.forEach((item)=>{
       value = item.find(ele => ele ===val );
       if (value !== undefined ){
         this.searchRes = item; 
         this.isShow = true;
         this.showSearch = "YES";
         this.showAll = false;
         console.log('found! ')
       }
    }); 
      
    // console.log(this.searchRes);
    
      
},
  handleSubmit(e){
    console.log('form submitted!');


  },

},
  computed:{
    checkYear: function(){
      var year = new Date().getFullYear(); //get the current year;
      var result = false; 
      
      if((this.year > year || this.year < 1000) || (typeof(this.year) != 'number')){
        result = true;
      } 
      if (this.year === ''){
        result = false;
      } 
    // console.log(typeof(this.year));
    // console.log(result);
  return result;
    },
    checkTitle: function(){
      return this.title.length > 0 && this.title.length<2 
    },
   
    checkAuthor: function(){
      return (this.author.length > 0 && this.author.length<2)
    },
    displayNewNote: function(){
      var showNewNote = [];
      for(var i=0; i< this.newNote.length; i++){
        let display = this.noteItem[i] + this.newNote[i];
        showNewNote.push(display);
      }
      
      return showNewNote;
      // console.log(showNewNote);
    },

    displayAllNotes: function(){
      // var x = 0;
      // var allNotes = []
      // // var aaa = this.noteItem[2] + this.notes[0][2];
      // while(x < this.noteItem.length){
      //   for(var i=0; i<this.notes.length; i++ ){
      //     let displayAll = "No." + (i+1) + ' :' + this.noteItem[i] + this.notes[x][i];
          
      //     console.log(displayAll);
      //     // allNotes.push(displayAll);
      //   };
      //   x += 1;
      // }
      
      // return allNotes;
    },

    displaySearch: function(){
      var handlearray = [];
      for(var i=0; i<this.noteItem.length; i++){
        let display = this.noteItem[i] + this.searchRes[i]; 
        handlearray.push(display);
      }
      // console.log(handlearray)
      return handlearray;
    },

    isDisable: function(){
      var result = false;
      if(!(this.title && this.author && this.year)){
        result = true;
      } 
      if(this.checkYear == true && this.checkTitle == true && this.checkAuthor == true){
        result = true
      }
      return result
    }
  }, 
  
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.container {
  min-width: 100%;
  min-height: 90vh;
  /* border: solid; */
  background-color: rgba(95, 158, 160, 0.886);
  display:inline-block;
  align-items: center;
  }

form {
    min-width: 20%;
    margin: 8vh 5vh 5vh 30vh;
    background: white;
    text-align: left;
    display:inline-flex;
    padding: 40px;
    /* border:solid; */
    border-radius: 10px;
  }

.errorMsg{
  color:rgba(139, 0, 0, 0.839);
  /* border: solid; */

}
  .leftbox {
  min-width: 45%;
  /* border: solid; */
  padding: 15px;
  /* background-color: chocolate; */
}
  

  .rightbox-content{
  min-width: 400px;
  min-height: 70vh;
  margin-left: 10px;
  margin-top: 50px;
  border: solid 1.2px;
  padding: 15px;
  background-color:cadetblue;
  opacity: 92%;
  color: white;
  letter-spacing: 1px;
  border-radius: 25px;
  }

  .box1 {
    padding: 5px;
    margin: 2px;
    width: max-content;
  }

  .box2 {
    margin-top: 10vh; 
    padding: 5px;
    font-size: 19px;
    /* border: solid; */
    
  }
  .box2 .deleteSearch {
    background-color: white;
    color:cadetblue;
    margin-bottom: 10px;
  }
label {
    color: #aaa;
    display: inline-block;
    margin: 25px 0 15px;
    font-size: 1em;
    text-transform: uppercase;
    letter-spacing: 1px;
    font-weight: bold;
  }
  input {
    display: block;
    padding: 10px 6px;
    width: 100%;
    box-sizing: border-box;
    border: none;
    border-bottom: 1px solid #ddd;
    margin-bottom: 6px;
    color: #555;
  }
  input[type="checkbox"] {
    display: inline-block;
    width: 16px;
    margin: 0 10px 0 0;
    position: relative;
    top: 2px;
  }
  .sill {
    display: inline-block;
    margin: 20px 10px 0 0;
    padding: 6px 12px;
    background: #eee;
    border-radius: 20px;
    font-size: 12px;
    letter-spacing: 1px;
    font-weight: bold;
    color: #777;
    cursor: pointer;
    
  }
  input[type="searchtext"] {
    display: inline-block;
    width: 70%;
    box-sizing: content-box;
    border: none;
    border-bottom: 1px solid #ddd;
    color: #555;
  }

  .search {
    margin: 30px auto;
  }

  #searchbutton {
    margin-left:10px;
    display: inline-block;
    background-color:cadetblue;
    color: white;
    border-radius: 20px;
    font-size: 17px;
    }

  #searchbutton:hover {
    cursor: pointer;
    background-color:rgba(95, 158, 160, 0.558);
  }
  .searchBox {
    cursor: pointer;
    padding: 5px;  
    margin-top:2px;
     
  }
  .showItem {
    color:darkred;
    margin-left: 5px;
    margin-top: 5px;
    padding: 5px;
    font-size: 15px;
    
  }
button {
    background: cadetblue;
    border: 0;
    padding: 10px 10px;
    margin-top: 20px;
    margin-right: 10px;
    color: white;
    border-radius: 20px;
    font-size: 17px;
  }
button:hover {
  cursor: pointer;
  background-color:rgba(95, 158, 160, 0.558);
}

 button[disabled] {
  background-color:cornsilk;
  color: grey;
  cursor:none;
}
  
</style>
