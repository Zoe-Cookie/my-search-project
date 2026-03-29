<script setup>
import { ref, computed } from 'vue'
import { SEARCH_DATASET } from './data/hw2_search_dataset.js'

const searchQuery = ref('')
const items = ref(SEARCH_DATASET)
const sortType = ref('title')

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
      <li v-for="item in processedItems" :key="item.id">
        <span>{{ item.url_path }}</span>
        <span>{{ item.url }}</span>
        <span>{{ item.title }}</span>
        <span>{{ item.text }}</span>
        <span>{{ item.created_at }}</span>
        <span>{{ item.popularity }}</span>
      </li>

      <li v-if="processedItems.length === 0" style="text-align: center; color: #888">
        找不到相關內容 😢
      </li>
    </ul>
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
  color: rgb(60, 92, 134);
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
  border: none;
  background-color: #555557;
  padding: 15px;
  width: 500px;
  border-radius: 30px;
  font-size: larger;
  transition: background-color 0.3s ease;
}

input:hover {
  background-color: #3d3d3f;
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

.item-list {
  padding: 0;
}

li {
  width: calc(80% - 20px);
  padding: 20px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.05); /* 淡淡的陰影 */
  display: flex;
  flex-direction: column;
  gap: 8px;
  text-align: left;
}

li span:nth-child(3) {
  font-size: 1.2rem;
  font-weight: bold;
  color: #2c3e50;
}

li span:nth-child(4) {
  color: #666;
  font-size: 0.95rem;
  line-height: 1.5;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  line-clamp: 3;
  overflow: hidden;
}

li span {
  overflow-wrap: break-word;
  word-break: break-word;
}

.avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  border: 2px solid white;
}
</style>
