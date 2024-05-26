
<template>
<EmitAja />
</template>

<script setup>
import EmitAja from './components/cobaemit.vue'
</script>


<script>
export default {
  data() {
    return {
      taskInput: '',
      taskList: [],
      selectedTasks: [],
      editing: [],
      selectedTask: null,
      filterCompleted: false,
      isToDoFormVisible: false,
      isPost: false,
      showwelcome:false,
      showMessage: false
    }
  },
  created() {
    // Load taskList from local storage when component is created
    const savedTasks = localStorage.getItem('taskList');
    if (savedTasks) {
      this.taskList = JSON.parse(savedTasks);
    }
  },
  watch: {
    // Watch for changes in taskList and save to local storage
    taskList: {
      handler(newValue) {
        localStorage.setItem('taskList', JSON.stringify(newValue));
      },
      deep: true
    }
  },
  computed: {
    totalTasks() {
      return this.taskList.length;
    },
    filteredTasks() {
      if (this.filterCompleted) {
        return this.taskList.filter(task => task.completed);
      } else {
        return this.taskList.filter(task => !task.completed);
      }
    }
  },
  methods: {
    addTask() {
      if (this.taskInput.trim() !== '') {
        const newTask = { text: this.taskInput.trim(), image: null, completed: false };
        this.taskList.push(newTask);
        this.editing.push(false);
        this.taskInput = '';
      }
    },
    toggleToDoFormVisibility() {
    this.isToDoFormVisible = !this.isToDoFormVisible;
    
  },
  toggleToPost(){
    this.isPost = !this.isPost;

  },
  hideToDoForm() {
  this.isToDoFormVisible = false;
},
hidePost() {
  this.isPost = false;
},
showPost(){
  this.isPost = true;

},
    showToDoForm() {
  // Mengatur tampilan formulir to-do list ketika tombol ditekan
  this.isToDoFormVisible = true;
},
showwelcomeopen(){
  this.showwelcome = true;
},
showWelcomeMessage() {
      this.showMessage = true;
    },
    cancelTask() {
      this.taskInput = ''; 
    },
    deleteTask(index) {
      if (this.selectedTask === this.taskList[index]) {
        this.selectedTask = null;
      }
      this.taskList.splice(index, 1);
      this.editing.splice(index, 1);
    },
    clearAllTasks() {
      this.taskList = [];
      this.selectedTasks = [];
      this.editing = [];
    },
    deleteSelectedTasks() {
      this.selectedTasks.sort((a, b) => b - a);
      this.selectedTasks.forEach(index => {
        this.taskList.splice(index, 1);
        this.editing.splice(index, 1);
      });
      this.selectedTasks = [];
    },
    toggleEdit(index) {
      this.editing[index] = !this.editing[index];
    },
    doneEditing(index) {
      this.editing[index] = false;
    },
    handleImageUpload(index) {
      const file = event.target.files[0];
      const reader = new FileReader();
      reader.onload = () => {
        const imageDataUrl = reader.result;
        if (/^data:image\//.test(imageDataUrl)) {
          this.taskList[index].image = imageDataUrl;
        } else {
          alert('Please upload an image file.');
        }
      };
      reader.readAsDataURL(file);
    },
    toggleCompleted(index) {
      this.taskList[index].completed = !this.taskList[index].completed;
    },
    toggleFilterCompleted() {
      this.filterCompleted = !this.filterCompleted;
    }
    ,
    viewTask(index)  {
  if (this.filterCompleted) {
    const completedTasks = this.taskList.filter(task => task.completed);
    this.selectedTask = { ...completedTasks[index], originalIndex: index };
  } else {
    const unfinishedTasks = this.taskList.filter(task => !task.completed);
    this.selectedTask = { ...unfinishedTasks[index], originalIndex: index };
  }
},

  }
}
</script>


<style>
body{
  display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    background: url('/src/assets/image/JEPANGMALAM.jpg') no-repeat; 
    background-size: cover;
    background-position: center;
}

.welcome-open{
  font-size: 70px;
  border-radius: 20px;
}
.myweb{
  color: #0cc215;
  text-shadow: black;
}
.showkontent{
  font-size: 20px;
}
.t{
  color: #45a049;
}
h1{
  color: #45a049;
}
span{
  color: #45a049;
}
input[type="text"]{
  color: #45a049;
}
.container {
  text-align: center;
  font-family: Arial, sans-serif;
  background-color: #d1f9d1;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
}
.footer {

  color: #4caf50;
text-shadow: #fff;
box-shadow: #45a049;
  
}
.footer a{
  color: #fff;
  text-shadow: black;
}
  .footer a:hover{
  color: rgba(10, 250, 50, 0.664);}
input[type="text"] {
  padding: 8px;
  margin-right: 8px;
}

input[type="checkbox"]:checked {
  background-color: #4caf50;
}

button {
  padding: 8px 16px;
  background-color: #4caf50;
  color: white;
  border: none;
  cursor: pointer;
  margin-left: 5px;
}

button:hover {
  background-color: #45a049;
}

ul {
  list-style-type: none;
  padding: 0;
}

.task-item {
  margin-top: 10px;
  background-color: #fff;
  padding: 10px;
  border-radius: 5px;
  box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
}
body{
  background-color: #d1f9f7f7;
}
.task-item.selected {
  transition: background-color 0.2s ease, transform 0.2s ease;
}

.task-item.selected:nth-child(odd) {
  background-color: #fff;
}

.task-item.selected:nth-child(even) {
  background-color: #f2f2f2;
}

.task-item.selected:hover {
  transform: scale(1.05);
}

.task-item input[type="text"] {
  padding: 8px;
}

.task-item img {
  margin-right: 5px;
}

.task-item input[type="file"] {
  margin-left: 5px;
}
.completed {
  text-decoration: line-through;
}
.button-group{
  margin-top: 5px;
}
/* tablet, dll */
@media only screen and (max-width: 768px) {
  h1{
  color: #45a049;
}
span{
  color: #45a049;
}
input[type="text"]{
  color: #45a049;
}
  .container {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .container input[type="text"] {
    margin-bottom: 10px; /* Menambahkan margin bawah untuk input Add Task */
  }
  button.cancel{
    margin-top: 5px;
    margin-bottom: 5px;
  }
  button.clearalltask{
    margin-bottom: 5px;
  }
  
  .button-group {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
  }

  .button-group button {
    margin: 5px;
  }

  .button-group button:first-child {
    margin-right: auto;
  }
}
</style>
