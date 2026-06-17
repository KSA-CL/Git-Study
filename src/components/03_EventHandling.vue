<!--
  📌 03. 이벤트 핸들링 & 메서드
  
  핵심 개념:
  - @click (v-on:click) : 클릭 이벤트
  - @submit.prevent : 폼 제출 + 기본 동작 방지
  - @keyup.enter : 키보드 이벤트 수식어
  - $event : 네이티브 이벤트 객체 접근
  - 이벤트 수식어: .stop, .prevent, .once, .self
-->
<script setup>
import { ref, reactive } from 'vue'

// reactive(): 객체를 반응형으로 만들 때 사용 (ref와 비교)
// ref는 .value로 접근, reactive는 직접 접근
const mousePos = reactive({ x: 0, y: 0 })
const clicks = ref(0)
const lastEvent = ref('아직 이벤트 없음')
const eventLog = ref([])

function logEvent(type, detail = '') {
  const entry = {
    id: Date.now(),
    type,
    detail,
    time: new Date().toLocaleTimeString('ko-KR'),
  }
  eventLog.value.unshift(entry)
  if (eventLog.value.length > 8) eventLog.value.pop()
  lastEvent.value = `${type}: ${detail}`
}

// 클릭 이벤트
function handleClick() {
  clicks.value++
  logEvent('click', `${clicks.value}번째 클릭`)
}

// 더블 클릭
function handleDblClick() {
  logEvent('dblclick', '더블 클릭 감지!')
}

// 마우스 이동 (트래커 영역)
function handleMouseMove(event) {
  mousePos.x = event.offsetX
  mousePos.y = event.offsetY
}

// 키보드 이벤트
const typedText = ref('')
function handleKeyup(event) {
  logEvent('keyup', `키: "${event.key}"`)
}

function handleEnter() {
  logEvent('keyup.enter', `입력값: "${typedText.value}"`)
  typedText.value = ''
}

// 카운터 (이벤트 수식어 데모)
const counter = ref(0)

function clearLog() {
  eventLog.value = []
  lastEvent.value = '로그 초기화됨'
}
</script>

<template>
  <div class="demo-section">
    <div class="section-header">
      <span class="section-number">03</span>
      <div>
        <h2>이벤트 핸들링</h2>
        <p class="section-desc">@click, @keyup, 이벤트 수식어, reactive() 실습</p>
      </div>
    </div>

    <div class="demo-grid">
      <!-- 인터랙션 영역 -->
      <div class="demo-card">
        <h3>🎯 이벤트 놀이터</h3>

        <!-- 마우스 트래커 -->
        <div
          class="mouse-tracker"
          @mousemove="handleMouseMove"
        >
          <div
            class="tracker-dot"
            :style="{
              left: mousePos.x + 'px',
              top: mousePos.y + 'px'
            }"
          ></div>
          <span class="tracker-label">
            마우스 위치: ({{ mousePos.x }}, {{ mousePos.y }})
          </span>
        </div>

        <!-- 클릭 버튼들 -->
        <div class="button-row">
          <!-- @click : 기본 클릭 이벤트 -->
          <button class="btn btn-click" @click="handleClick">
            👆 클릭 ({{ clicks }})
          </button>
          <!-- @dblclick : 더블 클릭 이벤트 -->
          <button class="btn btn-dbl" @dblclick="handleDblClick">
            👆👆 더블클릭
          </button>
          <!-- @click.once : 한 번만 실행 -->
          <button
            class="btn btn-once"
            @click.once="logEvent('click.once', '이 버튼은 한 번만 동작!')"
          >
            🔒 .once
          </button>
        </div>

        <!-- 키보드 이벤트 -->
        <div class="input-group">
          <label>⌨️ 키보드 이벤트 (@keyup, @keyup.enter)</label>
          <input
            v-model="typedText"
            type="text"
            placeholder="타이핑하면 키 이벤트 발생, Enter로 전송"
            @keyup="handleKeyup"
            @keyup.enter="handleEnter"
          />
        </div>

        <!-- 이벤트 수식어 카운터 -->
        <div class="modifier-demo">
          <h4>이벤트 수식어 데모</h4>
          <div class="counter-row">
            <button
              class="btn btn-counter"
              @click="counter--"
            >−</button>
            <span class="counter-value">{{ counter }}</span>
            <button
              class="btn btn-counter"
              @click="counter++"
            >+</button>
          </div>
          <p class="hint-text">
            💡 인라인으로도 핸들러 작성 가능: <code>@click="counter++"</code>
          </p>
        </div>
      </div>

      <!-- 이벤트 로그 -->
      <div class="demo-card log-card">
        <div class="log-header">
          <h3>📜 이벤트 로그</h3>
          <button class="btn btn-clear" @click="clearLog">초기화</button>
        </div>
        <div class="last-event">
          마지막: <strong>{{ lastEvent }}</strong>
        </div>
        <TransitionGroup name="log" tag="div" class="log-list">
          <div
            v-for="entry in eventLog"
            :key="entry.id"
            class="log-entry"
          >
            <span class="log-time">{{ entry.time }}</span>
            <span class="log-type">{{ entry.type }}</span>
            <span class="log-detail">{{ entry.detail }}</span>
          </div>
        </TransitionGroup>
        <div v-if="eventLog.length === 0" class="empty-log">
          이벤트를 발생시켜 보세요!
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
  background: linear-gradient(135deg, var(--color-accent), #ff8fc4);
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

/* Mouse Tracker */
.mouse-tracker {
  position: relative;
  height: 120px;
  background: var(--color-bg-input);
  border-radius: var(--radius-md);
  margin-bottom: var(--space-md);
  overflow: hidden;
  cursor: crosshair;
  display: flex;
  align-items: center;
  justify-content: center;
}

.tracker-dot {
  position: absolute;
  width: 16px;
  height: 16px;
  background: var(--color-accent);
  border-radius: 50%;
  transform: translate(-50%, -50%);
  pointer-events: none;
  box-shadow: 0 0 16px var(--color-accent-glow);
  transition: left 0.05s, top 0.05s;
}

.tracker-label {
  font-size: 0.75rem;
  color: var(--color-text-muted);
  font-family: 'Courier New', monospace;
  pointer-events: none;
}

/* Buttons */
.button-row {
  display: flex;
  gap: var(--space-sm);
  margin-bottom: var(--space-md);
  flex-wrap: wrap;
}

.btn {
  padding: 8px 16px;
  border: none;
  border-radius: var(--radius-sm);
  font-family: var(--font-family);
  font-size: 0.82rem;
  font-weight: 600;
  cursor: pointer;
  transition: all var(--transition-fast);
}

.btn-click {
  background: var(--color-primary);
  color: white;
}

.btn-click:hover {
  background: var(--color-primary-light);
  box-shadow: var(--shadow-glow-primary);
}

.btn-dbl {
  background: var(--color-accent);
  color: white;
}

.btn-dbl:hover {
  box-shadow: 0 0 16px var(--color-accent-glow);
}

.btn-once {
  background: var(--color-surface);
  color: var(--color-text-secondary);
}

.btn-once:hover {
  color: var(--color-text);
}

/* Input */
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

.input-group input {
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

.input-group input:focus {
  border-color: var(--color-accent);
  box-shadow: 0 0 0 3px var(--color-accent-glow);
}

/* Modifier Demo */
.modifier-demo {
  background: var(--color-bg-input);
  border-radius: var(--radius-md);
  padding: var(--space-md);
}

.modifier-demo h4 {
  font-size: 0.85rem;
  font-weight: 600;
  margin-bottom: var(--space-sm);
  color: var(--color-text-secondary);
}

.counter-row {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: var(--space-md);
  margin-bottom: var(--space-sm);
}

.btn-counter {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: var(--color-surface);
  color: var(--color-text);
  font-size: 1.2rem;
  display: flex;
  align-items: center;
  justify-content: center;
}

.btn-counter:hover {
  background: var(--color-primary);
}

.counter-value {
  font-size: 2rem;
  font-weight: 800;
  min-width: 60px;
  text-align: center;
  color: var(--color-accent);
}

.hint-text {
  font-size: 0.75rem;
  color: var(--color-text-muted);
  text-align: center;
}

.hint-text code {
  background: rgba(108, 99, 255, 0.2);
  padding: 1px 6px;
  border-radius: 3px;
}

/* Log Card */
.log-card {
  display: flex;
  flex-direction: column;
}

.log-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: var(--space-md);
}

.log-header h3 {
  margin-bottom: 0;
}

.btn-clear {
  background: var(--color-surface);
  color: var(--color-text-muted);
  font-size: 0.75rem;
  padding: 4px 12px;
}

.btn-clear:hover {
  color: var(--color-danger);
}

.last-event {
  padding: 8px 12px;
  background: var(--color-bg-input);
  border-radius: var(--radius-sm);
  font-size: 0.8rem;
  color: var(--color-text-secondary);
  margin-bottom: var(--space-md);
}

.log-list {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: var(--space-xs);
}

.log-entry {
  display: grid;
  grid-template-columns: auto auto 1fr;
  gap: var(--space-sm);
  padding: 6px 10px;
  background: var(--color-bg-input);
  border-radius: var(--radius-sm);
  font-size: 0.78rem;
  align-items: center;
}

.log-time {
  color: var(--color-text-muted);
  font-family: 'Courier New', monospace;
  font-size: 0.7rem;
}

.log-type {
  background: var(--color-primary);
  color: white;
  padding: 1px 8px;
  border-radius: 10px;
  font-size: 0.7rem;
  font-weight: 600;
}

.log-detail {
  color: var(--color-text-secondary);
}

.empty-log {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  color: var(--color-text-muted);
  font-size: 0.85rem;
}

/* TransitionGroup */
.log-enter-active,
.log-leave-active {
  transition: all 0.3s ease;
}

.log-enter-from {
  opacity: 0;
  transform: translateY(-10px);
}

.log-leave-to {
  opacity: 0;
  transform: translateX(20px);
}

@media (max-width: 768px) {
  .demo-grid {
    grid-template-columns: 1fr;
  }
}
</style>
