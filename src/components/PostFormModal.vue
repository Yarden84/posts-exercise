<script setup>
import { ref, watch, computed } from 'vue'

const props = defineProps({
  isOpen: {
    type: Boolean,
    required: true
  },
  mode: {
    type: String,
    required: true,
    validator: (value) => ['add', 'edit'].includes(value)
  },
  post: {
    type: Object,
    default: null
  }
})

const emit = defineEmits(['close', 'post-saved'])

const formData = ref({
  title: '',
  body: '',
  tags: []
})
const isSubmitting = ref(false)
const error = ref(null)
const newTag = ref('')

const modalTitle = computed(() => props.mode === 'edit' ? 'Edit Post' : 'Add New Post')
const submitButtonText = computed(() => {
  if (isSubmitting.value) {
    return props.mode === 'edit' ? 'Updating...' : 'Adding...'
  }
  return props.mode === 'edit' ? 'Update Post' : 'Add Post'
})

const resetForm = () => {
  formData.value = { title: '', body: '', tags: [] }
  newTag.value = ''
  error.value = null
}

const closeModal = () => {
  emit('close')
  resetForm()
}

const addTag = () => {
  const tag = newTag.value.trim().toLowerCase()
  if (tag && !formData.value.tags.includes(tag)) {
    formData.value.tags.push(tag)
    newTag.value = ''
  }
}

const removeTag = (index) => {
  formData.value.tags.splice(index, 1)
}

const handleTagKeydown = (event) => {
  if (event.key === 'Enter') {
    event.preventDefault()
    addTag()
  }
}

watch(() => props.post, (newPost) => {
  if (newPost && props.mode === 'edit') {
    formData.value = {
      title: newPost.title || '',
      body: newPost.body || '',
      tags: newPost.tags || []
    }
  } else {
    resetForm()
  }
}, { immediate: true })

watch(() => props.mode, () => {
  if (props.mode === 'add') {
    resetForm()
  }
})

const handleSubmit = async () => {
  if (!formData.value.title.trim() || !formData.value.body.trim()) {
    error.value = 'Please fill in both title and body'
    return
  }

  try {
    isSubmitting.value = true
    error.value = null
    
    if (props.mode === 'edit') {   
      if (props.post.new) {
        const updatedPost = {
          ...props.post,
          title: formData.value.title.trim(),
          body: formData.value.body.trim(),
          tags: formData.value.tags
        }
        
        emit('post-saved', updatedPost)
        closeModal()
        return
      }
      
      const response = await fetch(`https://dummyjson.com/posts/${props.post.id}`, {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          title: formData.value.title.trim(),
          body: formData.value.body.trim(),
          tags: formData.value.tags
        })
      })

      if (!response.ok) {
        throw new Error('Failed to update post')
      }

      const savedPost = await response.json()
      emit('post-saved', savedPost)
      
    } else {
      const response = await fetch('https://dummyjson.com/posts/add', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          title: formData.value.title.trim(),
          body: formData.value.body.trim(),
          tags: formData.value.tags,
          userId: 1
        })
      })

      if (!response.ok) {
        throw new Error('Failed to add post')
      }

      const savedPost = await response.json()
      emit('post-saved', savedPost)
    }
    
    closeModal()
    
  } catch (err) {
    error.value = err.message
  } finally {
    isSubmitting.value = false
  }
}
</script>

<template>
  <div v-if="isOpen" class="modal-overlay" @click="closeModal">
    <div class="modal-content" @click.stop>
      <div class="modal-header">
        <h3>{{ modalTitle }}</h3>
        <button class="close-btn" @click="closeModal">&times;</button>
      </div>
      
      <form @submit.prevent="handleSubmit" class="post-form">
        <div class="form-group">
          <label for="title">Title</label>
          <input 
            id="title"
            v-model="formData.title"
            type="text" 
            placeholder="Enter post title"
            required
          />
        </div>
        
        <div class="form-group">
          <label for="body">Body</label>
          <textarea 
            id="body"
            v-model="formData.body"
            placeholder="Enter post content"
            rows="4"
            required
          ></textarea>
        </div>
        
        <div class="form-group">
          <label for="tags">Tags (Press Enter to add a tag)</label>
          <input 
            id="tags"
            v-model="newTag"
            type="text"
            placeholder="Enter a tag and press Enter"
            @keydown="handleTagKeydown"
          />
          
          <div v-if="formData.tags.length > 0" class="tags-display">
            <span 
              v-for="(tag, index) in formData.tags" 
              :key="index" 
              class="tag-item"
            >
              {{ tag }}
              <button 
                type="button" 
                class="remove-tag" 
                @click="removeTag(index)"
              >
              &times;
              </button>
            </span>
          </div>
        </div>
        
        <div v-if="error" class="form-error">
          {{ error }}
        </div>
        
        <div class="form-actions">
          <button type="button" @click="closeModal" class="cancel-btn">
            Cancel
          </button>
          <button type="submit" :disabled="isSubmitting" class="submit-btn">
            {{ submitButtonText }}
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal-content {
  background: white;
  border-radius: 8px;
  width: 90%;
  max-width: 500px;
  max-height: 90vh;
  overflow-y: auto;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.15);
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px 20px 10px 20px;
  border-bottom: 1px solid #eee;
  margin-bottom: 20px;

  h3 {
    margin: 0;
    font-size: 18px;
    font-weight: 700;
    color: rgb(37, 39, 51);
  }

  .close-btn {
    background: none;
    border: none;
    font-size: 24px;
    cursor: pointer;
    color: #999;
    padding: 0;
    width: 30px;
    height: 30px;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 50%;

    &:hover {
      background-color: #f5f5f5;
      color: #666;
    }
  }
}

.post-form {
  padding: 0 20px 20px 20px;

  .form-group {
    margin-bottom: 20px;

    label {
      display: block;
      margin-bottom: 8px;
      font-weight: 600;
      color: rgb(37, 39, 51);
      font-size: 14px;
    }

    input, textarea {
      width: 100%;
      padding: 12px;
      border: 1px solid #ddd;
      border-radius: 6px;
      font-size: 14px;
      font-family: inherit;
      transition: border-color 0.2s ease;

      &:focus {
        outline: none;
        border-color: rgb(54, 121, 255);
        box-shadow: 0 0 0 3px rgba(54, 121, 255, 0.1);
      }

      &::placeholder {
        color: #999;
      }
    }

    textarea {
      resize: vertical;
      min-height: 100px;
    }
  }

  .tags-display {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-top: 10px;
  }

  .form-error {
    color: #d32f2f;
    font-size: 14px;
    margin-bottom: 15px;
    padding: 10px;
    background-color: #ffebee;
    border-radius: 4px;
    border: 1px solid #ffcdd2;
  }

  .form-actions {
    display: flex;
    gap: 10px;
    justify-content: flex-end;

    button {
      padding: 10px 20px;
      border-radius: 6px;
      font-size: 14px;
      font-weight: 600;
      cursor: pointer;
      transition: all 0.2s ease;
      border: none;

      &.cancel-btn {
        background-color: #f5f5f5;
        color: #666;

        &:hover {
          background-color: #e0e0e0;
        }
      }

      &.submit-btn {
        background-color: rgb(54, 121, 255);
        color: white;

        &:hover:not(:disabled) {
          background-color: rgb(0, 85, 255);
        }

        &:disabled {
          background-color: #ccc;
          cursor: not-allowed;
        }
      }
    }
  }
}
</style> 