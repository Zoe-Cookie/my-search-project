<script setup>
import { ref, computed, watch } from 'vue'
import { SEARCH_DATASET } from './data/hw2_search_dataset.js'

const searchQuery = ref('')
const items = ref(SEARCH_DATASET)
const sortType = ref('title')

const currentPage = ref(1)
const pageSize = 10

const processedItems = computed(() => {
  let result = items.value

  if (searchQuery.value) {
    const query = searchQuery.value.toLowerCase()
    result = result.filter((item) => {
      return item.title.toLowerCase().includes(query) || item.text.toLowerCase().includes(query)
    })
  }

  return [...result].sort((a, b) => {
    if (sortType.value === 'title') {
      return a.title.localeCompare(b.title)
    } else if (sortType.value === 'latest') {
      return new Date(b.created_at) - new Date(a.created_at)
    } else if (sortType.value === 'popular') {
      return b.popularity - a.popularity
    }
    return 0
  })
})

const paginatedItems = computed(() => {
  const startIndex = (currentPage.value - 1) * pageSize
  const endIndex = startIndex + pageSize
  return processedItems.value.slice(startIndex, endIndex)
})

const totalPages = computed(() => {
  return Math.ceil(processedItems.value.length / pageSize) || 1
})

watch([searchQuery, sortType], () => {
  currentPage.value = 1
})
</script>

<template>
  <div class="container">
    <div class="top-bar">
      <h1>Zoe's Search</h1>
      <div class="search-box">
        <input v-model="searchQuery" placeholder="請輸入搜尋內容..." />
      </div>
      <img src="./assets/profile.webp" class="avatar" alt="User Avatar" />
    </div>

    <div class="list-header">
      <p>共找到 {{ processedItems.length }} 筆項目</p>
      <p class="sort">排序</p>
      <select v-model="sortType" class="sort-select">
        <option value="latest">依時間</option>
        <option value="popular">依人氣</option>
        <option value="title">依標題</option>
      </select>
    </div>

    <ul class="item-list">
      <li v-for="item in paginatedItems" :key="item.id">
        <span class="small">{{ item.url_path || '未知路徑' }}</span>
        <span class="small">{{ item.url || '無網址' }}</span>
        <a v-bind:href="item.url" target="_blank">{{ item.title || '無標題' }}</a>
        <span class="description">{{ item.text || '暫無內容提供。' }}</span>
        <span class="small">{{ item.created_at || '未知時間' }}</span>
        <span class="small">{{ item.popularity || 0 }}</span>
      </li>
    </ul>
    <div class="pagination" v-if="processedItems.length > 0">
      <button :disabled="currentPage === 1" @click="currentPage--">上一頁</button>

      <span class="page-info">第 {{ currentPage }} / {{ totalPages }} 頁</span>

      <button :disabled="currentPage === totalPages" @click="currentPage++">下一頁</button>
    </div>
  </div>
</template>

<style scoped>
.container {
  width: 80%;
  margin: 0 auto;
  padding: 10px;
}

.top-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #555557;
}

h1 {
  color: #3c5c86;
}

p {
  font-size: larger;
  display: flex;
}

.search-box {
  flex: 1;
  display: flex;
  justify-content: flex-start;
  padding-left: 50px;
}

input {
  border: 1px solid #555557;
  background-color: transparent;
  padding: 15px;
  width: 500px;
  border-radius: 30px;
  font-size: larger;
}

.avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  border: 2px solid white;
}

.list-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 20px;
}

.sort {
  flex: 1;
  justify-content: flex-end;
  margin-right: 10px;
}

.sort-select {
  padding: 8px 12px;
  font-size: 1rem;
  border-radius: 8px;
  background-color: transparent;
  cursor: pointer;
}

select option {
  background-color: #44444e;
  color: white;
  font-size: 1rem;
}

.item-list {
  padding: 0;
}

li {
  width: calc(80% - 20px);
  padding: 20px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.5);
  display: flex;
  flex-direction: column;
  gap: 8px;
  text-align: left;
}

.small {
  font-size: 0.8rem;
}

li a {
  font-size: 1.4rem;
  font-weight: bold;
  color: #3c5c86;
  text-decoration: none;
  align-self: flex-start;
}

li a:hover {
  text-decoration: underline;
  color: #5683bf;
}

.description {
  color: #666;
}

li span,
a {
  overflow-wrap: break-word;
  word-break: break-word;
}

.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 20px;
  margin: 30px 0;
}

.pagination button {
  padding: 8px 16px;
  border: none;
  background-color: #3c5c86;
  color: white;
  border-radius: 6px;
  cursor: pointer;
  font-size: 1rem;
}

.pagination button:disabled {
  background-color: #555557;
  cursor: not-allowed;
}

.page-info {
  font-size: 1.1rem;
  font-weight: bold;
}

/* ==========
   RWD 手機板
   ========== */
@media (max-width: 768px) {
  .container {
    width: 95%;
    padding: 5px;
  }

  .top-bar {
    display: grid;
    grid-template-columns: 1fr auto;
    grid-template-areas:
      'title avatar'
      'search search';
    gap: 15px;
    padding: 10px 0;
    border-bottom: 1px solid #555557;
  }

  h1 {
    grid-area: title;
  }

  .search-box {
    grid-area: search;
    padding-left: 0;
    display: flex;
  }

  input {
    width: 100%;
    box-sizing: border-box;
  }

  .avatar {
    grid-area: avatar;
  }

  li {
    width: 100%;
    padding: 15px;
    box-sizing: border-box;
  }
}
</style>
