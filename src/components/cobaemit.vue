<template>
     <div >
    <button class="welcome-open" v-if="!showwelcome"  @click="showwelcomeopen">Show Welcome TO Me</button>
  </div>
    <section class="pembukaweb" v-if="showwelcome">
  <h1 class="myweb">WELCOME TO MY WEB VUE AND VITE</h1>
  <button class="showkontent" v-if="!showMessage" @click="showWelcomeMessage">Show Konten Me</button>
  </section>
      <section class="konten" v-if="showMessage">
  <HeadersTitle />
  <button @click="toggleToPost">{{ isPost ? 'Hide Post' : 'Show Post' }}</button>
  <button @click="toggleToDoFormVisibility">{{ isToDoFormVisible ? 'Hide Todos' : 'Show Todos' }}</button><br><br>
  <section class="Post">
  <div v-if="isPost" class="container">
    <PostUser />
  </div>
</section>
  <section class="Todos">
    
  <div v-if="isToDoFormVisible"  class="container">
    <SlotCom />

    <input v-model="taskInput" type="text" placeholder="Add a task...">
    <button @click="addTask">Add Task</button>
    <button @click="cancelTask" class="cancel">Cancel</button>
    
    <button @click="clearAllTasks" class="clearalltask">Clear All Tasks</button>
    <button @click="deleteSelectedTasks" v-if="selectedTasks.length > 0">Delete Selected Tasks</button>
    <button @click="toggleFilterCompleted">{{ filterCompleted ? 'Show Unfinished' : 'Show Finished' }}</button>
    <div v-if="taskList.length === 0" style="color:#4caf50;">No tasks available.</div>
    <div class="t">Total Tasks: {{ totalTasks }}</div>
    <ul>
      <li v-for="(task, index) in filteredTasks" :key="index" class="task-item" :class="{ 'selected': selectedTasks.includes(index), 'completed': task.completed }">
        <input type="checkbox" v-model="selectedTasks" :value="index">
        <span v-if="!editing[index]" :style="{ 'text-decoration': task.completed ? 'line-through' : 'none' }">{{ task.text }}</span>
        <input v-else type="text" v-model="taskList[index].text" @keydown.enter="doneEditing(index)" @blur="doneEditing(index)">
        <input type="file" @change="handleImageUpload(index)">
        <img v-if="task.image" :src="task.image" alt="Task Image" style="max-width: 100px; max-height: 100px;">
        <div class="button-group">
          <button v-if="!task.completed" @click="toggleEdit(index)">
  {{ editing[index] ? 'Done' : 'Edit' }}
</button>


        <button @click="deleteTask(index)">Delete</button>
        <button @click="toggleCompleted(index)">{{ task.completed ? 'Mark as Not Finished' : 'Mark as Completed' }}</button>
        <button @click="viewTask(index)">View</button>  
      </div>
      </li>
    </ul>
    <div v-if="selectedTask !== null">
      <h2 style="color: #4caf50;">Data</h2>
      <p>{{ selectedTask.text }}</p>
      <img v-if="selectedTask.image" :src="selectedTask.image" alt="Task Image" style="max-width: 200px; max-height: 200px;">
      <button @click="selectedTask = null"><Span style="font-size: 20px; color: black;">X</Span></button>
    </div>
  </div>
</section>
<FooterMe />
</section>
    

</template>
<script setup>
import  PostUser from './UserPosts.vue'
import HeadersTitle from "./Header.vue"
import FooterMe from './Footer.vue'
import SlotCom  from './HeaderTodos.vue'
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