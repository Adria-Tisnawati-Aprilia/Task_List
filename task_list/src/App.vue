<script setup>
import {ref , onMounted, computed, watch } from 'vue'

const task = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const task_asc = computed(() => task.value.sort((a, b) => {
  return a.createdAt - b.createdAt
}))

const addTask = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }
  
  task.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    createdAt: new Date().getTime()
  })
  
  input_content.value = ''
  input_category.value = null
}

const removeTask = (task) => {
  task.value = task.value.filter((t) => t !== task)
}

watch(task, newVal => {
  localStorage.setItem('task', JSON.stringify(newVal))
}, 
  { deep: true}
)

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  task.value = JSON.parse(localStorage.getItem('task')) || []
})

</script>
<template>
  
  <main class="app">
    
    <section class="greeting">
      <h2 class="title">
          Alohaa, <input type="text" placeholder="Name here"
          v-model="name" />
      </h2>
    </section>
    
    <section class="create-task">
      <h3>CREATE A TASK </h3>
      
      <form id="new-task-form" @submit.prevent="addTask">
        <h4>What's  on your task list?</h4>
        <input 
          type="text" 
          id="content"
          placeholder="e.g. make a video "
          v-model="input_content" />
        
        <h4>Pick a category</h4>
        <div class="options">
        
          <label>
            <input 
              type="radio" 
              name="category" 
              id="category1"
              value="business"
              v-model="input_category" />
            <span class="bubble business"></span>
            <div>Businnes</div>
          </label>
          
          <label>
            <input 
              type="radio" 
              name="category" 
              id="category2"
              value="personal"
              v-model="input_personal" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        
        </div>
        
          <input type="submit" value="AddTask"/>
      </form>
    </section>
    
    <section class="task-list" >
      <h3>TASK LIST </h3>
        <div class="list" id="task-list">
        
          <div :v-for="task in task_asc" :class="`task-item ${task.done && 'done'}`">
            
            <label>
              <input type="checkbox" v-model="task.done" />
    						<span :class="`bubble ${
    							task.category == 'business' 
    								? 'business' 
    								: 'personal'
    						}`"></span>
    				</label>
    				
    				<div class="task-content">
    				  <input type="text" v-model="task.content"/>
    				</div>
    				
    				<div class="actions">
    				  <button class="delete" @click="removeTask(task)" >Delete</button>
    				</div>
    				
          </div>
      </div>
    </section>
  </main>
  
</template>
