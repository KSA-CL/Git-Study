<!--
  📌 01. 데이터 바인딩 (Data Binding)
  
  Vue의 가장 기본적인 개념:
  - {{ }} : 텍스트 보간 (Mustache 문법)
  - v-bind (:) : 속성 바인딩
  - v-model : 양방향 바인딩
  - ref() : 반응형 데이터 선언
  - computed() : 계산된 속성
-->
<script setup>
import { ref, computed } from 'vue'

// ref()로 반응형 변수 선언
// 값이 바뀌면 화면이 자동으로 업데이트됨
const name = ref('')
const age = ref(25)
const color = ref('#6c63ff')

// computed()는 의존하는 반응형 데이터가 변경될 때 자동으로 재계산
const greeting = computed(() => {
  if (!name.value) return '이름을 입력해 주세요 👋'
  return `안녕하세요, ${name.value}님! 🎉`
})

const birthYear = computed(() => {
  return new Date().getFullYear() - age.value
})

const isAdult = computed(() => age.value >= 18)
</script>

<template>
  <div class="demo-section">
    <div class="section-header">
      <span class="section-number">01</span>
      <div>
        <h2>데이터 바인딩</h2>
        <p class="section-desc">ref, computed, v-model, v-bind 실습</p>
      </div>
    </div>

    <div class="demo-grid">
      <!-- 입력 패널 -->
      <div class="demo-card">
        <h3>📝 입력 (v-model)</h3>
        <div class="input-group">
          <label for="input-name">이름</label>
          <!-- v-model: 양방향 바인딩. 입력하면 name.value가 즉시 업데이트 -->
          <input
            id="input-name"
            v-model="name"
            type="text"
            placeholder="이름을 입력하세요"
          />
        </div>
        <div class="input-group">
          <label for="input-age">나이: {{ age }}세</label>
          <!-- v-model은 다양한 input 타입에 사용 가능 -->
          <input
            id="input-age"
            v-model.number="age"
            type="range"
            min="1"
            max="100"
          />
        </div>
        <div class="input-group">
          <label for="input-color">테마 색상</label>
          <!-- v-bind(:style)로 동적 스타일 바인딩 -->
          <div class="color-picker-row">
            <input
              id="input-color"
              v-model="color"
              type="color"
            />
            <span class="color-value">{{ color }}</span>
          </div>
        </div>
      </div>

      <!-- 출력 패널 -->
      <div class="demo-card result-card">
        <h3>📺 출력 (텍스트 보간 & computed)</h3>
        <!-- {{ }}  텍스트 보간: JS 표현식 사용 가능 -->
        <div class="result-item">
          <span class="result-label">인사말</span>
          <span class="result-value highlight">{{ greeting }}</span>
        </div>
        <div class="result-item">
          <span class="result-label">이름 글자수</span>
          <span class="result-value">{{ name.length }}자</span>
        </div>
        <div class="result-item">
          <span class="result-label">출생년도 (computed)</span>
          <span class="result-value">{{ birthYear }}년</span>
        </div>
        <div class="result-item">
          <span class="result-label">성인 여부</span>
          <span
            class="badge"
            :class="isAdult ? 'badge-success' : 'badge-warning'"
          >
            {{ isAdult ? '✅ 성인' : '🔸 미성년' }}
          </span>
        </div>
        <!-- :style 로 동적 스타일 바인딩 -->
        <div
          class="color-preview"
          :style="{ backgroundColor: color, boxShadow: `0 0 20px ${color}40` }"
        >
          선택한 색상 미리보기
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
  background: linear-gradient(135deg, var(--color-primary), var(--color-primary-light));
  border-radius: var(--radius-md);
  font-size: 1.1rem;
  font-weight: 700;
  color: white;
  flex-shrink: 0;
}

.section-header h2 {
  font-size: 1.4rem;
  font-weight: 700;
  color: var(--color-text);
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
  color: var(--color-text);
}

.input-group {
  margin-bottom: var(--space-md);
}

.input-group label {
  display: block;
  font-size: 0.8rem;
  font-weight: 500;
  color: var(--color-text-secondary);
  margin-bottom: var(--space-xs);
}

.input-group input[type="text"] {
  width: 100%;
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

.input-group input[type="text"]:focus {
  border-color: var(--color-primary);
  box-shadow: 0 0 0 3px var(--color-primary-glow);
}

.input-group input[type="range"] {
  width: 100%;
  accent-color: var(--color-primary);
}

.color-picker-row {
  display: flex;
  align-items: center;
  gap: var(--space-sm);
}

.color-picker-row input[type="color"] {
  width: 40px;
  height: 32px;
  border: none;
  border-radius: var(--radius-sm);
  cursor: pointer;
  background: none;
}

.color-value {
  font-family: 'Courier New', monospace;
  font-size: 0.85rem;
  color: var(--color-text-secondary);
}

.result-card {
  display: flex;
  flex-direction: column;
  gap: var(--space-sm);
}

.result-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 12px;
  background: var(--color-bg-input);
  border-radius: var(--radius-sm);
}

.result-label {
  font-size: 0.8rem;
  color: var(--color-text-muted);
}

.result-value {
  font-weight: 600;
  font-size: 0.9rem;
}

.result-value.highlight {
  color: var(--color-primary-light);
}

.badge {
  padding: 2px 10px;
  border-radius: 20px;
  font-size: 0.8rem;
  font-weight: 600;
}

.badge-success {
  background: rgba(0, 212, 170, 0.15);
  color: var(--color-secondary);
}

.badge-warning {
  background: rgba(255, 200, 87, 0.15);
  color: var(--color-warning);
}

.color-preview {
  margin-top: var(--space-sm);
  padding: var(--space-md);
  border-radius: var(--radius-md);
  text-align: center;
  font-weight: 600;
  font-size: 0.85rem;
  color: white;
  transition: all var(--transition-normal);
}

@media (max-width: 768px) {
  .demo-grid {
    grid-template-columns: 1fr;
  }
}
</style>
