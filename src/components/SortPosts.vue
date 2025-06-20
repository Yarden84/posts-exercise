<script setup>
import { ref, watch, onMounted, onUnmounted } from 'vue'

const props = defineProps({
  sortBy: {
    type: String,
    default: 'id'
  },
  sortOrder: {
    type: String,
    default: 'desc',
    validator: (value) => ['asc', 'desc'].includes(value)
  }
})

const emit = defineEmits(['sort-change'])

const currentSortBy = ref(props.sortBy)
const currentSortOrder = ref(props.sortOrder)
const isDropdownOpen = ref(false)

const sortOptions = [
  { value: 'title', label: 'Title' },
  { value: 'views', label: 'Views' }
]

const handleSortByChange = (value) => {
  currentSortBy.value = value
  isDropdownOpen.value = false
  emitSortChange()
}

const toggleSortOrder = () => {
  currentSortOrder.value = currentSortOrder.value === 'asc' ? 'desc' : 'asc'
  emitSortChange()
}

const toggleDropdown = () => {
  isDropdownOpen.value = !isDropdownOpen.value
}

const closeDropdown = () => {
  isDropdownOpen.value = false
}

const emitSortChange = () => {
  emit('sort-change', {
    sortBy: currentSortBy.value,
    sortOrder: currentSortOrder.value
  })
}

const getCurrentLabel = () => {
  const option = sortOptions.find(opt => opt.value === currentSortBy.value)
  return option ? option.label : 'Title'
}

const handleClickOutside = (event) => {
  if (!event.target.closest('.sort-by-wrapper')) {
    closeDropdown()
  }
}

watch(() => props.sortBy, (newValue) => {
  currentSortBy.value = newValue
})

watch(() => props.sortOrder, (newValue) => {
  currentSortOrder.value = newValue
})

onMounted(() => {
  document.addEventListener('click', handleClickOutside)
})

onUnmounted(() => {
  document.removeEventListener('click', handleClickOutside)
})
</script>

<template>
  <div class="sort-container">
    <!-- Sort By Dropdown -->
    <div class="sort-by-wrapper">
      <label class="sort-label">Sort by:</label>
      <div class="custom-select">
        <button 
          class="select-trigger"
          @click="toggleDropdown"
          type="button"
        >
          <span class="selected-value">{{ getCurrentLabel() }}</span>
          <svg 
            class="select-arrow"
            :class="{ 'open': isDropdownOpen }"
            viewBox="0 0 24 24" 
            fill="currentColor"
          >
            <path d="M7 10l5 5 5-5z"/>
          </svg>
        </button>
        
      </div>
      <div 
        v-if="isDropdownOpen"
        class="dropdown-menu"
      >
        <button
          v-for="option in sortOptions"
          :key="option.value"
          class="dropdown-item"
          :class="{ 'active': currentSortBy === option.value }"
          @click="handleSortByChange(option.value)"
          type="button"
        >
          {{ option.label }}
        </button>
      </div>
    </div>

    <!-- Sort Order Button -->
    <button 
      @click="toggleSortOrder"
      class="sort-order-btn"
      type="button"
      :title="`Sort ${currentSortOrder === 'asc' ? 'Descending' : 'Ascending'}`"
    >
      <svg 
        class="sort-icon" 
        :class="{ 'asc': currentSortOrder === 'asc' }"
        viewBox="0 0 24 24" 
        fill="currentColor"
      >
        <path d="M7 14l5-5 5 5z"/>
      </svg>
    </button>
  </div>
</template>

<style lang="scss" scoped>
.sort-container {
  display: flex;
  align-items: center;
  gap: 12px;
  min-width: 200px;
}

.sort-by-wrapper {
  width: 370px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 8px;
  flex: 1;
  background-color: #fff;
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 8px 12px;
  transition: all 0.2s ease;
  position: relative;

  &:focus-within {
    border-color: rgb(54, 121, 255);
    box-shadow: 0 0 0 3px rgba(54, 121, 255, 0.1);
  }

  .sort-label {
    font-size: 14px;
    font-weight: 500;
    color: #666;
    white-space: nowrap;
  }

  .custom-select {
    position: relative;
    flex: 1;

    .select-trigger {
      width: 100%;
      display: flex;
      align-items: center;
      justify-content: space-between;
      background: transparent;
      border: none;
      outline: none;
      font-size: 14px;
      color: #333;
      cursor: pointer;
      padding: 4px 0;
      text-align: left;

      &:focus {
        outline: none;
      }

      .selected-value {
        font-weight: 500;
      }

      .select-arrow {
        width: 16px;
        height: 16px;
        color: #666;
        transition: transform 0.2s ease;
        flex-shrink: 0;

        &.open {
          transform: rotate(180deg);
        }
      }
    }
  }

  .dropdown-menu {
    position: absolute;
    top: 100%;
    left: 0;
    right: 0;
    background-color: #fff;
    border: 1px solid #ddd;
    border-radius: 8px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
    z-index: 1000;
    margin-top: 4px;
    overflow: hidden;
    animation: dropdownSlide 0.2s ease;

    .dropdown-item {
      width: 100%;
      padding: 12px 16px;
      background: none;
      border: none;
      text-align: left;
      font-size: 14px;
      color: #333;
      cursor: pointer;
      transition: all 0.2s ease;
      border-bottom: 1px solid #f0f0f0;

      &:last-child {
        border-bottom: none;
      }

      &:hover {
        background-color: #f8f9fa;
        color: rgb(54, 121, 255);
      }

      &.active {
        background-color: rgb(54, 121, 255);
        color: white;
        font-weight: 500;
      }

      &:focus {
        outline: none;
        background-color: #f8f9fa;
      }
    }
  }
}

.sort-order-btn {
  background-color: #fff;
  border: 1px solid #ddd;
  color: #666;
  cursor: pointer;
  padding: 8px;
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s ease;
  min-width: 40px;
  height: 40px;

  &:hover {
    background-color: #f5f5f5;
    color: rgb(54, 121, 255);
    border-color: rgb(54, 121, 255);
  }

  &:focus {
    outline: none;
    background-color: #f5f5f5;
    color: rgb(54, 121, 255);
    border-color: rgb(54, 121, 255);
    box-shadow: 0 0 0 3px rgba(54, 121, 255, 0.1);
  }

  .sort-icon {
    width: 18px;
    height: 18px;
    transition: transform 0.2s ease;

    &.asc {
      transform: rotate(180deg);
    }
  }
}

@keyframes dropdownSlide {
  from {
    opacity: 0;
    transform: translateY(-8px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style> 