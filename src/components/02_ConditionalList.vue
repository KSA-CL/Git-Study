<!--
  📌 02. 조건부 렌더링 & 리스트 렌더링
  
  핵심 디렉티브:
  - v-if / v-else-if / v-else : 조건부 렌더링 (DOM에서 제거)
  - v-show : 조건부 표시 (display:none으로 숨김)
  - v-for : 리스트 렌더링 (배열/객체 반복)
  - :key : v-for에서 각 요소의 고유 식별자 (성능 최적화 핵심!)
-->
<script setup>
import { ref, computed } from 'vue'

// 할 일 목록 데이터
const newTodo = ref('')
const filter = ref('all') // 'all', 'active', 'done'

const todos = ref([
  { id: 1, text: 'Vue ref() 이해하기', done: true },
  { id: 2, text: 'v-for 디렉티브 실습', done: false },
  { id: 3, text: 'computed 속성 활용', done: false },
])

let nextId = 4

// 할 일 추가
function addTodo() {
  const text = newTodo.value.trim()
  if (!text) return
  todos.value.push({
    id: nextId++,
    text,
    done: false,
  })
  newTodo.value = ''
}

// 할 일 토글
function toggleTodo(todo) {
  todo.done = !todo.done
}

// 할 일 삭제
function removeTodo(id) {
  todos.value = todos.value.filter(t => t.id !== id)
}

// 필터링된 목록 (computed)
const filteredTodos = computed(() => {
  if (filter.value === 'active') return todos.value.filter(t => !t.done)
  if (filter.value === 'done') return todos.value.filter(t => t.done)
  return todos.value
})

// 통계
const stats = computed(() => ({
  total: todos.value.length,
  done: todos.value.filter(t => t.done).length,
  active: todos.value.filter(t => !t.done).length,
}))

// v-show 데모용
const showHint = ref(false)
</script>

<template>
  <div class="demo-section">
    <div class="section-header">
      <span class="section-number">02</span>
      <div>
        <h2>조건부 & 리스트 렌더링</h2>
        <p class="section-desc">v-if, v-show, v-for, :key 실습</p>
      </div>
    </div>

    <div class="demo-grid">
      <!-- 할 일 입력 + v-show 데모 -->
      <div class="demo-card">
        <h3>📋 할 일 목록 (v-for + :key)</h3>

        <!-- 입력 폼 -->
        <form class="todo-form" @submit.prevent="addTodo">
          <input
            v-model="newTodo"
            type="text"
            placeholder="새 할 일 입력..."
            class="todo-input"
          />
          <button type="submit" class="btn btn-primary">추가</button>
        </form>

        <!-- v-show: CSS display로 토글 (DOM에 남아있음) -->
        <button
          class="btn btn-ghost"
          @click="showHint = !showHint"
        >
          {{ showHint ? '힌트 숨기기' : '💡 v-show 힌트 보기' }}
        </button>
        <div v-show="showHint" class="hint-box">
          <strong>v-show vs v-if 차이점:</strong><br />
          • <code>v-show</code>: display:none으로 숨김 (DOM에 존재)<br />
          • <code>v-if</code>: DOM에서 완전히 제거/추가<br />
          • 자주 토글되면 v-show, 조건이 바뀌지 않으면 v-if 사용
        </div>

        <!-- 필터 탭 -->
        <div class="filter-tabs">
          <button
            v-for="f in ['all', 'active', 'done']"
            :key="f"
            class="tab"
            :class="{ active: filter === f }"
            @click="filter = f"
          >
            {{ f === 'all' ? '전체' : f === 'active' ? '진행중' : '완료' }}
          </button>
        </div>

        <!-- v-for: 배열을 순회하며 렌더링 -->
        <!-- :key는 Vue가 각 요소를 추적할 수 있게 해줌 (필수!) -->
        <TransitionGroup name="list" tag="ul" class="todo-list">
          <li
            v-for="todo in filteredTodos"
            :key="todo.id"
            class="todo-item"
            :class="{ done: todo.done }"
          >
            <button
              class="todo-check"
              :class="{ checked: todo.done }"
              @click="toggleTodo(todo)"
            >
              {{ todo.done ? '✓' : '' }}
            </button>
            <span class="todo-text">{{ todo.text }}</span>
            <button
              class="todo-delete"
              @click="removeTodo(todo.id)"
            >
              ✕
            </button>
          </li>
        </TransitionGroup>

        <!-- v-if / v-else : 조건부 렌더링 -->
        <div v-if="filteredTodos.length === 0" class="empty-state">
          <span>🎉</span>
          <p v-if="filter === 'active'">진행중인 할 일이 없습니다!</p>
          <p v-else-if="filter === 'done'">완료된 할 일이 없습니다.</p>
          <p v-else>할 일을 추가해 보세요!</p>
        </div>
      </div>

      <!-- 통계 패널 -->
      <div class="demo-card">
        <h3>📊 통계 (v-if 조건분기)</h3>
        <div class="stats-grid">
          <div class="stat-box">
            <span class="stat-value">{{ stats.total }}</span>
            <span class="stat-label">전체</span>
          </div>
          <div class="stat-box stat-active">
            <span class="stat-value">{{ stats.active }}</span>
            <span class="stat-label">진행중</span>
          </div>
          <div class="stat-box stat-done">
            <span class="stat-value">{{ stats.done }}</span>
            <span class="stat-label">완료</span>
          </div>
        </div>

        <!-- 진행률 바 -->
        <div class="progress-section">
          <div class="progress-header">
            <span>진행률</span>
            <span v-if="stats.total > 0">
              {{ Math.round((stats.done / stats.total) * 100) }}%
            </span>
            <span v-else>0%</span>
          </div>
          <div class="progress-bar">
            <div
              class="progress-fill"
              :style="{
                width: stats.total > 0
                  ? `${(stats.done / stats.total) * 100}%`
                  : '0%'
              }"
            ></div>
          </div>
        </div>

        <!-- 상태별 메시지: v-if / v-else-if / v-else 체이닝 -->
        <div class="status-message">
          <template v-if="stats.total === 0">
            <span class="status-icon">📝</span>
            <p>아직 할 일이 없습니다. 위에서 추가해 보세요!</p>
          </template>
          <template v-else-if="stats.done === stats.total">
            <span class="status-icon">🏆</span>
            <p>모든 할 일을 완료했습니다! 대단해요!</p>
          </template>
          <template v-else-if="stats.done > stats.active">
            <span class="status-icon">💪</span>
            <p>절반 이상 완료! 거의 다 했어요!</p>
          </template>
          <template v-else>
            <span class="status-icon">🚀</span>
            <p>화이팅! 하나씩 해나가 봅시다!</p>
          </template>
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
  background: linear-gradient(135deg, var(--color-secondary), #00f0c0);
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
  grid-template-columns: 1.2fr 0.8fr;
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

/* Todo Form */
.todo-form {
  display: flex;
  gap: var(--space-sm);
  margin-bottom: var(--space-md);
}

.todo-input {
  flex: 1;
  padding: 10px 14px;
  background: var(--color-bg-input);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-sm);
  color: var(--color-text);
  font-family: var(--font-family);
  font-size: 0.9rem;
  outline: none;
  transition: border-color var(--transition-fast);
}

.todo-input:focus {
  border-color: var(--color-secondary);
  box-shadow: 0 0 0 3px var(--color-secondary-glow);
}

.btn {
  padding: 8px 16px;
  border: none;
  border-radius: var(--radius-sm);
  font-family: var(--font-family);
  font-size: 0.85rem;
  font-weight: 600;
  cursor: pointer;
  transition: all var(--transition-fast);
}

.btn-primary {
  background: var(--color-secondary);
  color: #0f0f1a;
}

.btn-primary:hover {
  background: #00f0c0;
  box-shadow: var(--shadow-glow-secondary);
}

.btn-ghost {
  background: transparent;
  color: var(--color-text-secondary);
  border: 1px dashed var(--color-border);
  width: 100%;
  margin-bottom: var(--space-md);
}

.btn-ghost:hover {
  color: var(--color-text);
  border-color: var(--color-text-muted);
}

/* Hint Box */
.hint-box {
  background: rgba(108, 99, 255, 0.1);
  border: 1px solid rgba(108, 99, 255, 0.2);
  border-radius: var(--radius-sm);
  padding: var(--space-md);
  font-size: 0.8rem;
  line-height: 1.8;
  color: var(--color-text-secondary);
  margin-bottom: var(--space-md);
}

.hint-box code {
  background: rgba(108, 99, 255, 0.2);
  padding: 1px 6px;
  border-radius: 3px;
  font-size: 0.8rem;
}

/* Filter Tabs */
.filter-tabs {
  display: flex;
  gap: var(--space-xs);
  margin-bottom: var(--space-md);
  background: var(--color-bg-input);
  padding: 3px;
  border-radius: var(--radius-sm);
}

.tab {
  flex: 1;
  padding: 6px 12px;
  border: none;
  border-radius: 4px;
  background: transparent;
  color: var(--color-text-muted);
  font-family: var(--font-family);
  font-size: 0.8rem;
  font-weight: 500;
  cursor: pointer;
  transition: all var(--transition-fast);
}

.tab.active {
  background: var(--color-secondary);
  color: #0f0f1a;
}

.tab:hover:not(.active) {
  color: var(--color-text);
}

/* Todo List */
.todo-list {
  list-style: none;
  display: flex;
  flex-direction: column;
  gap: var(--space-sm);
}

.todo-item {
  display: flex;
  align-items: center;
  gap: var(--space-sm);
  padding: 10px 12px;
  background: var(--color-bg-input);
  border-radius: var(--radius-sm);
  transition: all var(--transition-fast);
}

.todo-item.done {
  opacity: 0.5;
}

.todo-check {
  width: 22px;
  height: 22px;
  border: 2px solid var(--color-border);
  border-radius: 50%;
  background: transparent;
  color: var(--color-secondary);
  font-size: 0.7rem;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  transition: all var(--transition-fast);
}

.todo-check.checked {
  background: var(--color-secondary);
  border-color: var(--color-secondary);
  color: #0f0f1a;
}

.todo-text {
  flex: 1;
  font-size: 0.9rem;
}

.todo-item.done .todo-text {
  text-decoration: line-through;
}

.todo-delete {
  background: none;
  border: none;
  color: var(--color-text-muted);
  cursor: pointer;
  font-size: 0.8rem;
  padding: 4px;
  border-radius: 4px;
  transition: all var(--transition-fast);
  opacity: 0;
}

.todo-item:hover .todo-delete {
  opacity: 1;
}

.todo-delete:hover {
  color: var(--color-danger);
  background: rgba(255, 87, 87, 0.1);
}

/* Empty State */
.empty-state {
  text-align: center;
  padding: var(--space-lg);
  color: var(--color-text-muted);
}

.empty-state span {
  font-size: 2rem;
}

.empty-state p {
  margin-top: var(--space-sm);
  font-size: 0.85rem;
}

/* Stats */
.stats-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: var(--space-sm);
  margin-bottom: var(--space-lg);
}

.stat-box {
  text-align: center;
  padding: var(--space-md);
  background: var(--color-bg-input);
  border-radius: var(--radius-md);
}

.stat-value {
  display: block;
  font-size: 1.8rem;
  font-weight: 800;
  color: var(--color-text);
}

.stat-active .stat-value {
  color: var(--color-primary-light);
}

.stat-done .stat-value {
  color: var(--color-secondary);
}

.stat-label {
  font-size: 0.75rem;
  color: var(--color-text-muted);
  margin-top: 2px;
}

/* Progress Bar */
.progress-section {
  margin-bottom: var(--space-lg);
}

.progress-header {
  display: flex;
  justify-content: space-between;
  font-size: 0.8rem;
  color: var(--color-text-secondary);
  margin-bottom: var(--space-xs);
}

.progress-bar {
  height: 8px;
  background: var(--color-bg-input);
  border-radius: 4px;
  overflow: hidden;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, var(--color-secondary), #00f0c0);
  border-radius: 4px;
  transition: width var(--transition-slow);
}

/* Status Message */
.status-message {
  text-align: center;
  padding: var(--space-lg);
  background: var(--color-bg-input);
  border-radius: var(--radius-md);
}

.status-icon {
  font-size: 2rem;
  display: block;
  margin-bottom: var(--space-sm);
}

.status-message p {
  font-size: 0.85rem;
  color: var(--color-text-secondary);
}

/* TransitionGroup animations */
.list-enter-active,
.list-leave-active {
  transition: all 0.3s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(-20px);
}
.list-move {
  transition: transform 0.3s ease;
}

@media (max-width: 768px) {
  .demo-grid {
    grid-template-columns: 1fr;
  }
}
</style>
