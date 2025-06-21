<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  placeholder: {
    type: String,
    default: 'Search posts...'
  }
})

const emit = defineEmits(['search'])

const searchQuery = ref('')
const isSearching = ref(false)

let searchTimeout = null

const handleSearch = () => {
  isSearching.value = true
  
  if (searchTimeout) {
    clearTimeout(searchTimeout)
  }
  
  searchTimeout = setTimeout(() => {
    emit('search', searchQuery.value.trim())
    isSearching.value = false
  }, 300)
}

const clearSearch = () => {
  searchQuery.value = ''
  emit('search', '')
}

watch(searchQuery, () => {
  handleSearch()
})
</script>

<template>
  <div class="search-container">
    <div class="search-input-wrapper">
      <svg class="search-icon" viewBox="0 0 24 24" fill="currentColor">
        <path d="M15.5 14h-.79l-.28-.27C15.41 12.59 16 11.11 16 9.5 16 5.91 13.09 3 9.5 3S3 5.91 3 9.5 5.91 16 9.5 16c1.61 0 3.09-.59 4.23-1.57l.27.28v.79l5 4.99L20.49 19l-4.99-5zm-6 0C7.01 14 5 11.99 5 9.5S7.01 5 9.5 5 14 7.01 14 9.5 11.99 14 9.5 14z"/>
      </svg>
      
      <input
        v-model="searchQuery"
        type="text"
        :placeholder="placeholder"
        class="search-input"
      />
      
      <button 
        v-if="searchQuery" 
        @click="clearSearch"
        class="clear-btn"
        type="button"
      >
        ×
      </button>
      
      <div v-if="isSearching" class="search-spinner">
        <div class="spinner"></div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.search-container {
  width: 300px;
  
  @media (max-width: 768px) {
    width: 100%;
  }
  
  .search-input-wrapper {
    position: relative;
    display: flex;
    align-items: center;
    background-color: #fff;
    border: 1px solid #ddd;
    border-radius: 8px;
    padding: 0 12px;
    transition: all 0.2s ease;
  
    &:focus-within {
      border-color: var(--color-primary-light);
      box-shadow: 0 0 0 3px var(--color-primary-light-2);
    }
  
    .search-icon {
      width: 18px;
      height: 18px;
      color: #999;
      margin-right: 8px;
      flex-shrink: 0;
    }
  
    .search-input {
      flex: 1;
      border: none;
      outline: none;
      padding: 12px 0;
      font-size: 14px;
      background: transparent;
      color: #333;
  
      &::placeholder {
        color: #999;
      }
    }
  
    .clear-btn {
      background: none;
      border: none;
      color: #999;
      font-size: 18px;
      cursor: pointer;
      padding: 4px;
      border-radius: 50%;
      width: 24px;
      height: 24px;
      display: flex;
      align-items: center;
      justify-content: center;
      transition: all 0.2s ease;
      margin-left: 8px;
  
      &:hover {
        background-color: #f5f5f5;
        color: #666;
      }
    }
  
    .search-spinner {
      margin-left: 8px;
      
      .spinner {
        width: 16px;
        height: 16px;
        border: 2px solid #f3f3f3;
        border-top: 2px solid var(--color-primary-light);
        border-radius: 50%;
        animation: spin 1s linear infinite;
      }
    }
  }
}


@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
</style> 