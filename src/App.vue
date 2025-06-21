<script setup>
import { ref } from 'vue'
import Navbar from './components/Navbar.vue'
import PostsList from './components/PostsList.vue'

const searchQuery = ref('')
const sortConfig = ref({ sortBy: 'id', sortOrder: 'asc' })

const handleSearch = (query) => {
  searchQuery.value = query
}

const handleSortChange = (config) => {
  sortConfig.value = config
}
</script>

<template>
  <div class="app-container">
    <Navbar 
      :sort-by="sortConfig.sortBy"
      :sort-order="sortConfig.sortOrder"
      @search="handleSearch"
      @sort-change="handleSortChange"
    />
    
    <div class="main-container">
      <PostsList 
        :search-query="searchQuery"
        :sort-config="sortConfig"
      />
    </div>
  </div>
</template>

<style scoped>
.app-container {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  
  .main-container {
    padding-top: 20px;
  }
}
</style>
