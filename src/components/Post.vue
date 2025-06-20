<script setup>
import { computed } from 'vue'

const props = defineProps({
  post: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['edit'])

const handleEdit = () => {
  emit('edit', props.post)
}

const likes = computed(() => props.post.reactions?.likes || 0)
const dislikes = computed(() => props.post.reactions?.dislikes || 0)
const views = computed(() => props.post.views || 0)
</script>

<template>
  <div class="post-container">
    <div class="post-top">
        <h3 class="post-title">{{ post.title }}</h3>

        <svg class="edit-post-btn" viewBox="0 0 15 15" fill="none" xmlns="http://www.w3.org/2000/svg" @click="handleEdit">
            <path d="M11.3873 4.08817C11.9129 4.61049 11.9129 5.45759 11.3873 5.98326L10.6708 6.69978L8.29688 4.32254L9.01339 3.60603C9.53572 3.08371 10.3828 3.08371 10.9085 3.60603L11.3873 4.08817ZM4.01451 8.57478L7.53683 5.08259L9.91406 7.45647L6.39174 10.9788C6.25446 11.1161 6.08036 11.2132 5.89286 11.26L3.88058 11.7656C3.69643 11.8092 3.50558 11.7556 3.37165 11.625C3.23806 11.4911 3.18482 11.2969 3.23036 11.0859L3.73326 9.10045C3.78013 8.91295 3.87723 8.74219 4.01451 8.57478ZM0 2.14286C0 0.959263 0.959263 0 2.14286 0H12.8571C14.0391 0 15 0.959263 15 2.14286V12.8571C15 14.0391 14.0391 15 12.8571 15H2.14286C0.959263 15 0 14.0391 0 12.8571V2.14286ZM1.60714 2.14286V12.8571C1.60714 13.1518 1.84687 13.3929 2.14286 13.3929H12.8571C13.1518 13.3929 13.3929 13.1518 13.3929 12.8571V2.14286C13.3929 1.84687 13.1518 1.60714 12.8571 1.60714H2.14286C1.84687 1.60714 1.60714 1.84687 1.60714 2.14286Z" fill="#83888F"/>
        </svg>
    </div>
    
    <p class="post-body">{{ post.body }}</p>
    
    <div v-if="post.tags && post.tags.length > 0" class="post-tags">
      <span 
        v-for="tag in post.tags" 
        :key="tag" 
        class="tag-item"
      >
        {{ tag }}
      </span>
    </div>

    <div class="post-stats">
      <div class="reactions">
        <div class="reaction-item">
          <svg viewBox="0 0 512 512" class="reaction-icon" xmlns="http://www.w3.org/2000/svg"><title/><g data-name="1" ><path d="M348.45,432.7H261.8a141.5,141.5,0,0,1-49.52-8.9l-67.5-25.07a15,15,0,0,1,10.45-28.12l67.49,25.07a111.79,111.79,0,0,0,39.08,7h86.65a14.21,14.21,0,1,0,0-28.42,15,15,0,0,1,0-30H368.9a14.21,14.21,0,1,0,0-28.42,15,15,0,0,1,0-30h20.44a14.21,14.21,0,0,0,10.05-24.26,14.08,14.08,0,0,0-10.05-4.16,15,15,0,0,1,0-30h20.45a14.21,14.21,0,0,0,10-24.26,14.09,14.09,0,0,0-10-4.17H268.15A15,15,0,0,1,255,176.74a100.2,100.2,0,0,0,9.2-29.33c3.39-21.87-.79-41.64-12.42-58.76a12.28,12.28,0,0,0-22.33,7c.49,51.38-23.25,88.72-68.65,108a15,15,0,1,1-11.72-27.61c18.72-8,32.36-19.75,40.55-35.08,6.68-12.51,10-27.65,9.83-45C199.31,77,211,61,229.18,55.34s36.81.78,47.45,16.46c24.71,36.36,20.25,74.1,13.48,97.21H409.79a44.21,44.21,0,0,1,19.59,83.84,44.27,44.27,0,0,1-20.44,58.42,44.27,44.27,0,0,1-20.45,58.43,44.23,44.23,0,0,1-40,63Z"/><path d="M155,410.49H69.13a15,15,0,0,1-15-15V189.86a15,15,0,0,1,15-15H155a15,15,0,0,1,15,15V395.49A15,15,0,0,1,155,410.49Zm-70.84-30H140V204.86H84.13Z"/></g></svg>
          <span class="reaction-count">{{ likes }}</span>
        </div>
        <div class="reaction-item">

          <svg viewBox="0 0 512 512" class="reaction-icon dislike" xmlns="http://www.w3.org/2000/svg"><title/><g data-name="1"><path d="M348.45,432.7H261.8a141.5,141.5,0,0,1-49.52-8.9l-67.5-25.07a15,15,0,0,1,10.45-28.12l67.49,25.07a111.79,111.79,0,0,0,39.08,7h86.65a14.21,14.21,0,1,0,0-28.42,15,15,0,0,1,0-30H368.9a14.21,14.21,0,1,0,0-28.42,15,15,0,0,1,0-30h20.44a14.21,14.21,0,0,0,10.05-24.26,14.08,14.08,0,0,0-10.05-4.16,15,15,0,0,1,0-30h20.45a14.21,14.21,0,0,0,10-24.26,14.09,14.09,0,0,0-10-4.17H268.15A15,15,0,0,1,255,176.74a100.2,100.2,0,0,0,9.2-29.33c3.39-21.87-.79-41.64-12.42-58.76a12.28,12.28,0,0,0-22.33,7c.49,51.38-23.25,88.72-68.65,108a15,15,0,1,1-11.72-27.61c18.72-8,32.36-19.75,40.55-35.08,6.68-12.51,10-27.65,9.83-45C199.31,77,211,61,229.18,55.34s36.81.78,47.45,16.46c24.71,36.36,20.25,74.1,13.48,97.21H409.79a44.21,44.21,0,0,1,19.59,83.84,44.27,44.27,0,0,1-20.44,58.42,44.27,44.27,0,0,1-20.45,58.43,44.23,44.23,0,0,1-40,63Z"/><path d="M155,410.49H69.13a15,15,0,0,1-15-15V189.86a15,15,0,0,1,15-15H155a15,15,0,0,1,15,15V395.49A15,15,0,0,1,155,410.49Zm-70.84-30H140V204.86H84.13Z"/></g></svg>
          <span class="reaction-count">{{ dislikes }}</span>
        </div>
      </div>
      
      <div class="views">
        <svg class="views-icon" viewBox="0 0 24 24" fill="currentColor">
          <path d="M12 4.5C7 4.5 2.73 7.61 1 12c1.73 4.39 6 7.5 11 7.5s9.27-3.11 11-7.5c-1.73-4.39-6-7.5-11-7.5zM12 17c-2.76 0-5-2.24-5-5s2.24-5 5-5 5 2.24 5 5-2.24 5-5 5zm0-8c-1.66 0-3 1.34-3 3s1.34 3 3 3 3-1.34 3-3-1.34-3-3-3z"/>
        </svg>
        <span class="views-count">{{ views }}</span>
      </div>
    </div>
  </div>
</template>

<style scoped>
    .post-container {
        width: 100%;
        background-color: #fff;
        padding: 10px;
        border: 1px solid rgba(227, 232, 235, 1);
        box-shadow: 0px 1px 9px 2px rgba(193, 194, 198, 0.15);
        border-radius: 8px;

        .post-top {
            display: flex;
            justify-content: space-between;
            align-items: baseline;
            
            .post-title {
              font-size: 16px;
              font-weight: 700;
              color: rgb(37, 39, 51);
              margin-bottom: 10px;
              font-family: Raleway;
              line-height: 1.2;
              letter-spacing: 0.08px;
            }
    
            .edit-post-btn {
                width: 15px;
                height: 15px;
                flex: auto 0 0;
                cursor: pointer;

                &:hover {
                    path {
                        fill: #000;
                    }
                }
            }
        }

        p {
            font-size: 14px;
            font-weight: 400;
            color: rgb(131, 136, 143);
            overflow: hidden;
            text-overflow: ellipsis;
            display: -webkit-box;
            -webkit-line-clamp: 3;
            -webkit-box-orient: vertical;
            margin-bottom: 10px;
        }

        .post-tags {
            display: flex;
            flex-wrap: wrap;
            gap: 6px;
            margin-top: 8px;
            margin-bottom: 12px;
        }

        .post-stats {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding-top: 12px;
            border-top: 1px solid #f0f0f0;
            margin-top: 8px;

            .reactions {
                display: flex;
                gap: 16px;

                .reaction-item {
                    display: flex;
                    align-items: center;
                    gap: 4px;
                    cursor: pointer;
                    padding: 4px 8px;
                    border-radius: 16px;
                    transition: all 0.2s ease;

                    &:hover {
                        background-color: #f5f5f5;
                    }

                    .reaction-icon {
                        width: 16px;
                        height: 16px;
                        transition: all 0.2s ease;

                        &.dislike {
                            transform: rotateX(180deg);
                        }
                    }

                    .reaction-count {
                        font-size: 12px;
                        font-weight: 600;
                        color: #666;
                    }
                }
            }

            .views {
                display: flex;
                align-items: center;
                gap: 4px;
                color: #999;
                font-size: 12px;

                .views-icon {
                    width: 14px;
                    height: 14px;
                }

                .views-count {
                    font-weight: 500;
                }
            }
        }
    }
</style>
