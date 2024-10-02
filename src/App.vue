<script setup>
import { ref, onMounted} from 'vue'

const newItem = ref({
  name: '',
  done: false
})

const items = ref([
  
])

const fetchItems = () =>{
  fetch('http://127.0.0.1:8000/api/tasks')
    .then(response => response.json())
    .then(data => items.value = data)
    .catch(error => console.log(error))
}

onMounted(() => {
  fetchItems()
})

const addItem = () => {
  if (newItem.value.name.trim() !== '') {
    fetch('http://127.0.0.1:8000/api/tasks', {
      method: 'POST',
      body: JSON.stringify(newItem.value),
      headers: { 'Content-Type': 'application/json' }
    })
    .then(response => response.json())
    .then(data => items.value.push(data["task"]))
    .catch(error => console.log(error))
    newItem.value.name = '' 
  }
}

const updateItem = ($item) => {
  $item.done = !$item.done
  if ($item.name.trim() !== '') {
    fetch('http://127.0.0.1:8000/api/tasks/' + $item.id + '', {
      method: 'PUT',
      body: JSON.stringify($item),
      headers: { 'Content-Type': 'application/json' }
    })
    .then(response => response.json())
    .catch(error => console.log(error))
  }
}

const deleteItem = ($item) => {
  fetch('http://127.0.0.1:8000/api/tasks/' + $item.id + '', {
    method: 'DELETE',
    headers: { 'Content-Type': 'application/json' }
  })
  .then(response => response.json())
  .catch(error => console.log(error))
  items.value = items.value.filter(item => item.id !== $item.id)
}


</script>

<template>
  <div class="grid grid-cols-1 h-[100px]">

    <h1 class="text-3xl text-white items-center justify-center flex">Todo List</h1>

  </div>
  <div class="grid grid-cols-12 h-100 mx-10 gap-5">

    <input type="text"
    @keyup.enter="addItem"
      class="p-4 text-xl text-white items-center justify-center flex bg-white/5 col-span-12 sm:col-span-10 rounded-lg focus:outline-none focus:ring focus:ring-white/20"
      v-model="newItem.name" />
    <button
      @click="addItem"
      class="text-xl text-white items-center justify-center  col-span-12 py-4 flex bg-indigo-500 sm:col-span-2 rounded-lg"
      :class="{ 'opacity-40': newItem.name.trim() === '' }">Add
      Item</button>
  </div>
  <br>
  <hr class="h-px my-4 bg-white/5 border-0">
  <div class="grid grid-cols-12 h-1 mx-10 gap-5">
    <p v-if="items.length === 0" class="text-3xl text-white/50 items-center justify-center col-span-12 p-4 text-center ">No Items</p>
    <ul class="col-span-12">
      <li v-for="item in items" :key="index">
        <div class="flex">
          <p class="text-xl text-white items-center justify-center flex-1 sm:col-span-10 p-4 w-10 break-words" :class="{ 'line-through': item.done, 'text-white/30': item.done }">{{ item.name }}</p>
        <button class="text-xl text-green-500 items-center justify-center sm:col-span-2 p-4" :class="{'text-white/30': item.done}" @click="updateItem(item)">{{ item.done ? 'Undo' : 'Done' }}</button>
        <button class="text-xl text-red-500 items-center justify-center sm:col-span-2 p-4" @click="deleteItem(item)">Delete</button>
        </div>
        <hr class="h-px my-4 bg-white/10 border-0">
      </li>
    </ul>
  </div>

</template>
