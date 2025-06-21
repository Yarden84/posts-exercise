<script setup>
import { ref, onMounted, onUnmounted, watch } from 'vue'
import Post from './Post.vue'
import PostFormModal from './PostFormModal.vue'

const props = defineProps({
  searchQuery: {
    type: String,
    default: ''
  },
  sortConfig: {
    type: Object,
    default: () => ({ sortBy: 'id', sortOrder: 'asc' })
  }
})

const posts = ref([])
const loading = ref(false)
const error = ref(null)
const currentPage = ref(1)
const hasMorePosts = ref(true)
const limit = 20

const modalState = ref({
  isOpen: false,
  mode: 'add', 
  post: null
})

const fetchPosts = async (page = 1, append = false) => {
  try {
    error.value = '';
    loading.value = true;
    const skip = (page - 1) * limit;

    
    let url = props.searchQuery ? `https://dummyjson.com/posts/search?q=${encodeURIComponent(props.searchQuery)}` : `https://dummyjson.com/posts?limit=${limit}&skip=${skip}`
    
    
    if (props.sortConfig && !props.searchQuery) {
      console.log("props.sortConfig");
      console.log(props.sortConfig);
      
      url += `&sortBy=${props.sortConfig.sortBy}&order=${props.sortConfig.sortOrder}`
    }
    
    const response = await fetch(url)
    
    if (!response.ok) {
      throw new Error('Failed to fetch posts')
    }
    
    const data = await response.json()
    const newPosts = data.posts || []
    
    if (append) {
      posts.value = [...posts.value, ...newPosts]
    } else {
      posts.value = newPosts
    }
    
    hasMorePosts.value = newPosts.length === limit && (posts.value.length < data.total)
    
  } catch (err) {
    error.value = err.message
  } finally {
    loading.value = false
  }
}

const loadMorePosts = async () => {
  if (loading.value || !hasMorePosts.value) return
  
  currentPage.value++
  await fetchPosts(currentPage.value, true)
}

const handleScroll = () => {
  const scrollTop = document.documentElement.scrollTop
  const windowHeight = window.innerHeight
  const documentHeight = document.documentElement.scrollHeight
  
  if (scrollTop + windowHeight >= documentHeight * 0.9) {
    loadMorePosts()
  }
}

watch(() => props.searchQuery, () => {
  currentPage.value = 1
  hasMorePosts.value = true
  fetchPosts(1, false)
})

watch(() => props.sortConfig, () => {
  if (!props.searchQuery) {
    currentPage.value = 1
    hasMorePosts.value = true
    fetchPosts(1, false)
  }
}, { deep: true })

const openAddModal = () => {
  modalState.value = {
    isOpen: true,
    mode: 'add',
    post: null
  }
}

const openEditModal = (post) => {
  modalState.value = {
    isOpen: true,
    mode: 'edit',
    post
  }
}

const closeModal = () => {
  modalState.value = {
    isOpen: false,
    mode: 'add',
    post: null
  }
}

const handlePostSaved = (savedPost) => {
  if (modalState.value.mode === 'add') {
    posts.value = [{...savedPost, new: true}, ...posts.value]
  } else {
    const index = posts.value.findIndex(post => post.id === savedPost.id)
    if (index !== -1) {
      posts.value[index] = savedPost
    }
  }
}

onMounted(() => {
  fetchPosts()
  window.addEventListener('scroll', handleScroll)
})

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll)
})
</script>

<template>
  <div class="posts-list">
    <div class="post-list-top">
        <h2 class="post-list-title">Posts</h2>
        <div class="add-post-btn" @click="openAddModal">Add post</div>
    </div>
    
    <div v-if="error" class="error">
      Error: {{ error }}
    </div>
    
    <div v-else-if="posts.length === 0 && !loading" class="no-posts">
      {{ searchQuery ? 'No posts found matching your search.' : 'No posts found.' }}
    </div>
    
    <Post 
    v-else
        v-for="post in posts" 
        :key="post.id" 
        :post="post" 
        @edit="openEditModal"
    />
    
    <div v-if="loading" class="loading">
      Loading posts...
    </div>
    
    <div v-if="!hasMorePosts && posts.length > 0" class="no-more-posts">
      No more posts to load.
    </div>

    <PostFormModal 
      :is-open="modalState.isOpen"
      :mode="modalState.mode"
      :post="modalState.post"
      @close="closeModal"
      @post-saved="handlePostSaved"
    />
  </div>
</template>

<style lang="scss" scoped>
.posts-list {
  width: 400px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
  padding: 40px 0;
  margin: 20px auto;

  @media (max-width: 400px) {
    width: 100%;
    padding: 40px 10px;
  }

  .post-list-top {
    width: 100%;
    display: flex;
    justify-content: space-between;
    align-items: center;

    .post-list-title {
      font-size: 20px;
      font-weight: 700;
    }

    .add-post-btn {
        font-size: 14px;
        font-weight: 400;
        color: rgb(54, 121, 255);
        line-height: 1;
        border-bottom: 1px solid transparent;
        transition: all 0.2s ease;
        cursor: pointer;

        &:hover {
            color: rgb(0, 85, 255);
            border-bottom: 1px solid rgb(0, 85, 255);
        }
    }
  }
  
  .loading, .error, .no-posts, .no-more-posts {
    text-align: center;
    padding: 20px;
    font-size: 16px;
    color: rgb(131, 136, 143);
  }
  
  .error {
    color: #d32f2f;
  }
  
  .no-more-posts {
    font-style: italic;
  }
}
</style>
