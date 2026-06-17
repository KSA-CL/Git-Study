<!--
  📌 05. 라이프사이클 & Watch
  
  핵심 개념:
  - onMounted() : 컴포넌트가 DOM에 마운트된 후 실행
  - onUnmounted() : 컴포넌트가 DOM에서 제거될 때 실행
  - watch() : 특정 반응형 데이터의 변경을 감시
  - watchEffect() : 의존성을 자동 추적하며 즉시 실행
  - 타이머, API 호출 등 side effect 관리에 활용
-->
<script setup>
import { ref, watch, watchEffect, onMounted, onUnmounted } from 'vue'

// ===== 라이프사이클 데모 =====
const mountTime = ref('')
const elapsedSeconds = ref(0)
let timer = null

// onMounted: DOM이 준비된 후 실행됨
// API 호출, DOM 조작, 타이머 설정 등에 사용
onMounted(() => {
  mountTime.value = new Date().toLocaleTimeString('ko-KR')
  timer = setInterval(() => {
    elapsedSeconds.value++
  }, 1000)
})

// onUnmounted: 컴포넌트 제거 시 정리 작업
// 타이머 해제, 이벤트 리스너 제거 등 필수!
onUnmounted(() => {
  if (timer) clearInterval(timer)
})

// ===== Watch 데모 =====
const searchQuery = ref('')
const searchResults = ref([])
const isSearching = ref(false)

// watch(): 특정 값의 변화를 감시
// 변경 전후 값(newVal, oldVal)을 콜백으로 받음
watch(searchQuery, (newVal, oldVal) => {
  console.log(`검색어 변경: "${oldVal}" → "${newVal}"`)
})

// watchEffect(): 내부에서 사용된 반응형 데이터를 자동 추적
// 디바운스된 검색 시뮬레이션
let searchTimeout = null
watchEffect(() => {
  const query = searchQuery.value.trim()

  if (searchTimeout) clearTimeout(searchTimeout)

  if (!query) {
    searchResults.value = []
    isSearching.value = false
    return
  }

  isSearching.value = true

  // 디바운스: 입력 후 500ms 기다렸다가 검색
  searchTimeout = setTimeout(() => {
    // 실제로는 여기서 API 호출
    const mockData = [
      'Vue.js 기초', 'Vue Router 사용법', 'Vuex 상태관리',
      'Pinia 스토어', 'Composition API', 'Options API',
      'Vue 컴포넌트 설계', 'Vue 디렉티브', 'Vue Transition',
      'Vite 빌드 도구', 'TypeScript + Vue',
    ]
    searchResults.value = mockData.filter(item =>
      item.toLowerCase().includes(query.toLowerCase())
    )
    isSearching.value = false
  }, 500)
})

// ===== Watch (deep, immediate 옵션) =====
const settings = ref({
  theme: 'dark',
  fontSize: 14,
  notifications: true,
})

const changeLog = ref([])

// deep: true → 객체 내부 속성 변경도 감지
// immediate: true → 워처 생성 시 즉시 한 번 실행
watch(
  settings,
  (newVal) => {
    changeLog.value.unshift({
      time: new Date().toLocaleTimeString('ko-KR'),
      detail: `테마=${newVal.theme}, 폰트=${newVal.fontSize}px, 알림=${newVal.notifications ? 'ON' : 'OFF'}`,
    })
    if (changeLog.value.length > 5) changeLog.value.pop()
  },
  { deep: true }
)

const formattedTime = (seconds) => {
  const m = Math.floor(seconds / 60)
  const s = seconds % 60
  return `${String(m).padStart(2, '0')}:${String(s).padStart(2, '0')}`
}
</script>

<template>
  <div class="demo-section">
    <div class="section-header">
      <span class="section-number">05</span>
      <div>
        <h2>라이프사이클 & Watch</h2>
        <p class="section-desc">onMounted, onUnmounted, watch, watchEffect 실습</p>
      </div>
    </div>

    <div class="demo-grid">
      <!-- 라이프사이클 + 검색 -->
      <div class="demo-card">
        <h3>⏱ 라이프사이클 (onMounted)</h3>

        <div class="lifecycle-info">
          <div class="info-row">
            <span class="info-label">마운트 시각</span>
            <span class="info-value">{{ mountTime }}</span>
          </div>
          <div class="info-row">
            <span class="info-label">경과 시간</span>
            <span class="info-value timer">{{ formattedTime(elapsedSeconds) }}</span>
          </div>
        </div>

        <h3 style="margin-top: var(--space-lg)">🔍 검색 (watchEffect + 디바운스)</h3>
        <div class="search-box">
          <input
            v-model="searchQuery"
            type="text"
            placeholder="'vue' 또는 'api'를 검색해 보세요..."
            class="search-input"
          />
          <span v-if="isSearching" class="search-spinner">⏳</span>
        </div>

        <div class="search-results">
          <div v-if="searchQuery && !isSearching && searchResults.length === 0" class="no-results">
            검색 결과가 없습니다.
          </div>
          <TransitionGroup name="result" tag="ul" class="result-list">
            <li v-for="result in searchResults" :key="result" class="result-item">
              {{ result }}
            </li>
          </TransitionGroup>
        </div>
      </div>

      <!-- Watch (deep) 데모 -->
      <div class="demo-card">
        <h3>⚙️ Watch (deep 옵션)</h3>
        <p class="card-desc">객체 내부 속성 변경 감지</p>

        <div class="settings-form">
          <div class="setting-row">
            <label>테마</label>
            <div class="toggle-group">
              <button
                class="toggle-btn"
                :class="{ active: settings.theme === 'dark' }"
                @click="settings.theme = 'dark'"
              >🌙 다크</button>
              <button
                class="toggle-btn"
                :class="{ active: settings.theme === 'light' }"
                @click="settings.theme = 'light'"
              >☀️ 라이트</button>
            </div>
          </div>
          <div class="setting-row">
            <label>폰트 크기: {{ settings.fontSize }}px</label>
            <input
              v-model.number="settings.fontSize"
              type="range"
              min="10"
              max="24"
            />
          </div>
          <div class="setting-row">
            <label>알림</label>
            <button
              class="toggle-switch"
              :class="{ on: settings.notifications }"
              @click="settings.notifications = !settings.notifications"
            >
              <span class="toggle-knob"></span>
            </button>
          </div>
        </div>

        <h4 class="log-title">📋 변경 로그 (watch deep)</h4>
        <div class="change-log">
          <div
            v-for="(log, idx) in changeLog"
            :key="idx"
            class="log-entry"
          >
            <span class="log-time">{{ log.time }}</span>
            <span class="log-detail">{{ log.detail }}</span>
          </div>
          <div v-if="changeLog.length === 0" class="log-empty">
            설정을 변경해 보세요
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.demo-section {
  margin-bottom: var(--space-2xl);
}

.section-header {
  display: flex;
  align-items: center;
  gap: var(--space-md);
  margin-bottom: var(--space-lg);
}

.section-number {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 48px;
  height: 48px;
  background: linear-gradient(135deg, #e040fb, #ea80fc);
  border-radius: var(--radius-md);
  font-size: 1.1rem;
  font-weight: 700;
  color: white;
  flex-shrink: 0;
}

.section-header h2 {
  font-size: 1.4rem;
  font-weight: 700;
}

.section-desc {
  font-size: 0.85rem;
  color: var(--color-text-muted);
  margin-top: 2px;
}

.demo-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: var(--space-lg);
}

.demo-card {
  background: var(--color-bg-card);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  padding: var(--space-lg);
  transition: all var(--transition-normal);
}

.demo-card:hover {
  border-color: var(--color-border-active);
  background: var(--color-bg-card-hover);
}

.demo-card h3 {
  font-size: 1rem;
  font-weight: 600;
  margin-bottom: var(--space-md);
}

.card-desc {
  font-size: 0.8rem;
  color: var(--color-text-muted);
  margin-top: -12px;
  margin-bottom: var(--space-md);
}

/* Lifecycle Info */
.lifecycle-info {
  display: flex;
  flex-direction: column;
  gap: var(--space-sm);
}

.info-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 14px;
  background: var(--color-bg-input);
  border-radius: var(--radius-sm);
}

.info-label {
  font-size: 0.8rem;
  color: var(--color-text-muted);
}

.info-value {
  font-weight: 600;
  font-size: 0.9rem;
}

.info-value.timer {
  font-family: 'Courier New', monospace;
  color: #e040fb;
  font-size: 1.2rem;
}

/* Search */
.search-box {
  position: relative;
  margin-bottom: var(--space-md);
}

.search-input {
  width: 100%;
  padding: 10px 14px;
  padding-right: 36px;
  background: var(--color-bg-input);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-sm);
  color: var(--color-text);
  font-family: var(--font-family);
  font-size: 0.9rem;
  outline: none;
  transition: border-color var(--transition-fast);
}

.search-input:focus {
  border-color: #e040fb;
  box-shadow: 0 0 0 3px rgba(224, 64, 251, 0.2);
}

.search-spinner {
  position: absolute;
  right: 12px;
  top: 50%;
  transform: translateY(-50%);
  animation: spin 1s linear infinite;
}

@keyframes spin {
  to { transform: translateY(-50%) rotate(360deg); }
}

.search-results {
  min-height: 40px;
}

.result-list {
  list-style: none;
  display: flex;
  flex-direction: column;
  gap: var(--space-xs);
}

.result-item {
  padding: 8px 12px;
  background: var(--color-bg-input);
  border-radius: var(--radius-sm);
  font-size: 0.85rem;
  transition: all 0.2s;
}

.result-item:hover {
  background: var(--color-surface);
  padding-left: 16px;
}

.no-results {
  text-align: center;
  padding: var(--space-md);
  color: var(--color-text-muted);
  font-size: 0.85rem;
}

.result-enter-active,
.result-leave-active {
  transition: all 0.3s ease;
}
.result-enter-from {
  opacity: 0;
  transform: translateX(-10px);
}
.result-leave-to {
  opacity: 0;
  transform: translateX(10px);
}

/* Settings */
.settings-form {
  display: flex;
  flex-direction: column;
  gap: var(--space-md);
  margin-bottom: var(--space-lg);
}

.setting-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.setting-row label {
  font-size: 0.85rem;
  color: var(--color-text-secondary);
}

.setting-row input[type="range"] {
  width: 50%;
  accent-color: #e040fb;
}

.toggle-group {
  display: flex;
  gap: 4px;
  background: var(--color-bg-input);
  padding: 3px;
  border-radius: var(--radius-sm);
}

.toggle-btn {
  padding: 5px 12px;
  border: none;
  border-radius: 4px;
  background: transparent;
  color: var(--color-text-muted);
  font-family: var(--font-family);
  font-size: 0.78rem;
  cursor: pointer;
  transition: all var(--transition-fast);
}

.toggle-btn.active {
  background: #e040fb;
  color: white;
}

/* Toggle Switch */
.toggle-switch {
  width: 44px;
  height: 24px;
  border: none;
  border-radius: 12px;
  background: var(--color-surface);
  cursor: pointer;
  position: relative;
  transition: background var(--transition-fast);
}

.toggle-switch.on {
  background: #e040fb;
}

.toggle-knob {
  position: absolute;
  top: 3px;
  left: 3px;
  width: 18px;
  height: 18px;
  background: white;
  border-radius: 50%;
  transition: transform var(--transition-fast);
}

.toggle-switch.on .toggle-knob {
  transform: translateX(20px);
}

/* Change Log */
.log-title {
  font-size: 0.85rem;
  font-weight: 600;
  margin-bottom: var(--space-sm);
  color: var(--color-text-secondary);
}

.change-log {
  display: flex;
  flex-direction: column;
  gap: var(--space-xs);
}

.log-entry {
  padding: 6px 10px;
  background: var(--color-bg-input);
  border-radius: var(--radius-sm);
  font-size: 0.75rem;
  display: flex;
  gap: var(--space-sm);
  align-items: center;
}

.log-time {
  color: var(--color-text-muted);
  font-family: 'Courier New', monospace;
  flex-shrink: 0;
}

.log-detail {
  color: var(--color-text-secondary);
}

.log-empty {
  text-align: center;
  padding: var(--space-md);
  color: var(--color-text-muted);
  font-size: 0.8rem;
}

@media (max-width: 768px) {
  .demo-grid {
    grid-template-columns: 1fr;
  }
}
</style>
