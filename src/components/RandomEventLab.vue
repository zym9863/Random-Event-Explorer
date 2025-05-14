<template>
  <div class="random-event-lab card">
    <div class="lab-header">
      <h2>
        <span class="icon">🎲</span>
        随机事件模拟实验室
      </h2>
      <p class="lab-description">通过大量模拟来观察随机事件的统计规律</p>
    </div>

    <div class="controls-panel">
      <div class="control-group">
        <label for="eventType">
          <span class="control-icon">📊</span>
          选择事件类型:
        </label>
        <select id="eventType" v-model="selectedEvent">
          <option value="coinFlip">抛硬币</option>
          <option value="diceRoll">掷骰子</option>
          <option value="drawCard">抽牌</option>
        </select>
      </div>

      <div class="control-group">
        <label for="simulations">
          <span class="control-icon">🔄</span>
          模拟次数:
        </label>
        <input type="number" id="simulations" v-model.number="numberOfSimulations" min="1" />
      </div>

      <button @click="startSimulation" class="start-button">
        <span class="button-icon">▶</span>
        开始模拟
      </button>
    </div>

    <div v-if="results.length" class="results-display">
      <div class="results-tabs">
        <button
          class="tab-button"
          :class="{ 'active': activeTab === 'results' }"
          @click="activeTab = 'results'"
        >
          模拟结果
        </button>
        <button
          class="tab-button"
          :class="{ 'active': activeTab === 'frequency' }"
          @click="activeTab = 'frequency'"
        >
          频率统计
        </button>
        <button
          class="tab-button"
          :class="{ 'active': activeTab === 'chart' }"
          @click="activeTab = 'chart'"
        >
          图表分析
        </button>
      </div>

      <div class="tab-content">
        <!-- 模拟结果标签页 -->
        <div v-if="activeTab === 'results'" class="results-list">
          <h3>模拟结果 <span class="badge">显示前 {{ Math.min(results.length, 100) }} 条</span></h3>
          <div class="results-scroll">
            <ul>
              <li v-for="(result, index) in displayedResults" :key="index">
                <span class="result-number">{{ index + 1 }}</span>
                <span class="result-value">{{ result }}</span>
              </li>
            </ul>
          </div>
          <p v-if="results.length > 100" class="more-results">... 还有 {{ results.length - 100 }} 条结果未显示</p>
        </div>

        <!-- 频率统计标签页 -->
        <div v-if="activeTab === 'frequency'" class="frequency-stats">
          <h3>结果频率统计</h3>
          <div class="frequency-table">
            <div class="table-header">
              <div class="outcome-header">结果</div>
              <div class="count-header">次数</div>
              <div class="percent-header">百分比</div>
            </div>
            <div class="table-body">
              <div
                v-for="(count, outcome) in frequencies"
                :key="outcome"
                class="table-row"
              >
                <div class="outcome-cell">{{ outcome }}</div>
                <div class="count-cell">{{ count }}</div>
                <div class="percent-cell">{{ ((count / results.length) * 100).toFixed(2) }}%</div>
              </div>
            </div>
          </div>
        </div>

        <!-- 图表分析标签页 -->
        <div v-if="activeTab === 'chart'" class="chart-analysis">
          <h3>频率分布图</h3>
          <div class="chart">
            <div
              v-for="(count, outcome) in frequencies"
              :key="outcome"
              class="bar-item"
            >
              <div class="bar-label">{{ outcome }}</div>
              <div class="bar-container">
                <div
                  class="bar"
                  :style="{ width: (count / results.length) * 100 + '%' }"
                  :class="getBarColorClass(outcome)"
                >
                  {{ ((count / results.length) * 100).toFixed(1) }}%
                </div>
              </div>
            </div>
          </div>
          <div class="chart-info">
            <p>总模拟次数: <strong>{{ results.length }}</strong></p>
            <p>不同结果数: <strong>{{ Object.keys(frequencies).length }}</strong></p>
          </div>
        </div>
      </div>
    </div>

    <div v-else class="no-results">
      <p>👆 设置参数并点击"开始模拟"按钮来查看结果</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';

type EventType = 'coinFlip' | 'diceRoll' | 'drawCard';

// 状态管理
const selectedEvent = ref<EventType>('coinFlip');
const numberOfSimulations = ref<number>(100);
const results = ref<string[]>([]);
const frequencies = ref<Record<string, number>>({});
const activeTab = ref<'results' | 'frequency' | 'chart'>('results');
const isSimulating = ref<boolean>(false);

// 计算属性
const displayedResults = computed(() => results.value.slice(0, 100));

// 模拟函数
const simulateCoinFlip = (): string => {
  return Math.random() < 0.5 ? '正面' : '反面';
};

const simulateDiceRoll = (): string => {
  return (Math.floor(Math.random() * 6) + 1).toString();
};

const createDeck = (): string[] => {
  const suits = ['♥', '♦', '♣', '♠'];
  const ranks = ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K'];
  const deck: string[] = [];
  for (const suit of suits) {
    for (const rank of ranks) {
      deck.push(rank + suit);
    }
  }
  return deck;
};

let deck: string[] = createDeck();
const drawnCards: Set<string> = new Set();

const simulateDrawCard = (): string => {
  if (drawnCards.size === deck.length) {
    // 每次模拟都使用完整的牌组
  }
  const randomIndex = Math.floor(Math.random() * deck.length);
  return deck[randomIndex];
};

// 获取图表条形的颜色类
const getBarColorClass = (outcome: string): string => {
  if (selectedEvent.value === 'coinFlip') {
    return outcome === '正面' ? 'bar-primary' : 'bar-secondary';
  } else if (selectedEvent.value === 'diceRoll') {
    const num = parseInt(outcome);
    if (num <= 2) return 'bar-primary';
    if (num <= 4) return 'bar-secondary';
    return 'bar-accent';
  } else if (selectedEvent.value === 'drawCard') {
    if (outcome.includes('♥')) return 'bar-danger';
    if (outcome.includes('♦')) return 'bar-warning';
    if (outcome.includes('♣')) return 'bar-success';
    if (outcome.includes('♠')) return 'bar-primary';
  }
  return 'bar-primary';
};

// 开始模拟
const startSimulation = async () => {
  // 验证输入
  if (numberOfSimulations.value <= 0 || !Number.isInteger(numberOfSimulations.value)) {
    alert('模拟次数必须是正整数');
    return;
  }

  // 重置结果
  results.value = [];
  frequencies.value = {};
  isSimulating.value = true;

  try {
    const currentResults: string[] = [];
    const currentFrequencies: Record<string, number> = {};

    // 模拟过程
    for (let i = 0; i < numberOfSimulations.value; i++) {
      let outcome = '';
      switch (selectedEvent.value) {
        case 'coinFlip':
          outcome = simulateCoinFlip();
          break;
        case 'diceRoll':
          outcome = simulateDiceRoll();
          break;
        case 'drawCard':
          // 每次模拟重置牌组
          deck = createDeck();
          drawnCards.clear();
          outcome = simulateDrawCard();
          break;
        default:
          return;
      }
      currentResults.push(outcome);
      currentFrequencies[outcome] = (currentFrequencies[outcome] || 0) + 1;

      // 对于大量模拟，每100次更新一次UI以提高响应性
      if (i % 100 === 0 && i > 0) {
        await new Promise(resolve => setTimeout(resolve, 0));
      }
    }

    // 更新结果
    results.value = currentResults;
    frequencies.value = currentFrequencies;

    // 默认显示图表标签页
    activeTab.value = 'chart';
  } finally {
    isSimulating.value = false;
  }
};
</script>

<style scoped>
/* 主容器样式 */
.random-event-lab {
  padding: 0;
  border: none;
  border-radius: var(--radius-lg);
  margin-bottom: 0;
  background-color: var(--bg-card);
  box-shadow: var(--shadow-md);
  overflow: hidden;
  transition: all var(--transition-normal);
}

.random-event-lab:hover {
  box-shadow: var(--shadow-lg);
  transform: translateY(-5px);
}

/* 标题区域 */
.lab-header {
  padding: 1.5rem;
  background-image: linear-gradient(135deg, var(--primary-color), var(--secondary-color));
  color: white;
  text-align: center;
}

.lab-header h2 {
  margin: 0;
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
}

.icon {
  font-size: 1.5rem;
}

.lab-description {
  margin: 0.5rem 0 0;
  opacity: 0.9;
  font-size: 0.95rem;
}

/* 控制面板 */
.controls-panel {
  padding: 1.5rem;
  background-color: rgba(0, 0, 0, 0.03);
  border-bottom: 1px solid var(--border-color);
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
  align-items: center;
}

.control-group {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  flex: 1;
  min-width: 150px;
}

.control-icon {
  margin-right: 0.5rem;
}

.control-group label {
  font-weight: 600;
  color: var(--text-color);
  font-size: 0.9rem;
}

.control-group select,
.control-group input {
  padding: 0.6rem 0.8rem;
  border-radius: var(--radius-sm);
  border: 1px solid var(--border-color);
  background-color: white;
  font-size: 1rem;
  transition: all var(--transition-fast);
}

.control-group select:focus,
.control-group input:focus {
  border-color: var(--primary-color);
  box-shadow: 0 0 0 3px rgba(58, 134, 255, 0.2);
  outline: none;
}

.start-button {
  padding: 0.6rem 1.2rem;
  background-color: var(--primary-color);
  color: white;
  border: none;
  border-radius: var(--radius-md);
  font-weight: 600;
  cursor: pointer;
  transition: all var(--transition-fast);
  display: flex;
  align-items: center;
  gap: 0.5rem;
  margin-left: auto;
}

.start-button:hover {
  background-color: var(--primary-hover);
  transform: translateY(-2px);
  box-shadow: var(--shadow-md);
}

.button-icon {
  font-size: 0.8rem;
}

/* 结果显示区域 */
.results-display {
  padding: 1.5rem;
}

/* 标签页导航 */
.results-tabs {
  display: flex;
  border-bottom: 1px solid var(--border-color);
  margin-bottom: 1.5rem;
}

.tab-button {
  padding: 0.8rem 1.2rem;
  background: none;
  border: none;
  border-bottom: 3px solid transparent;
  color: var(--text-light);
  font-weight: 600;
  cursor: pointer;
  transition: all var(--transition-fast);
}

.tab-button:hover {
  color: var(--primary-color);
}

.tab-button.active {
  color: var(--primary-color);
  border-bottom-color: var(--primary-color);
}

/* 标签页内容 */
.tab-content {
  min-height: 300px;
}

/* 模拟结果列表 */
.results-list h3 {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

.badge {
  font-size: 0.8rem;
  background-color: var(--primary-color);
  color: white;
  padding: 0.2rem 0.5rem;
  border-radius: var(--radius-sm);
  font-weight: normal;
}

.results-scroll {
  max-height: 300px;
  overflow-y: auto;
  border: 1px solid var(--border-color);
  border-radius: var(--radius-sm);
  background-color: white;
}

.results-list ul {
  list-style-type: none;
  padding: 0;
  margin: 0;
}

.results-list li {
  padding: 0.6rem 1rem;
  border-bottom: 1px solid var(--border-color);
  display: flex;
  align-items: center;
}

.results-list li:last-child {
  border-bottom: none;
}

.result-number {
  font-weight: 600;
  color: var(--primary-color);
  width: 40px;
}

.result-value {
  flex: 1;
}

.more-results {
  text-align: center;
  font-style: italic;
  color: var(--text-light);
  margin-top: 0.5rem;
}

/* 频率统计表格 */
.frequency-stats h3 {
  margin-bottom: 1rem;
}

.frequency-table {
  border: 1px solid var(--border-color);
  border-radius: var(--radius-sm);
  overflow: hidden;
}

.table-header {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  background-color: var(--primary-color);
  color: white;
  font-weight: 600;
  padding: 0.8rem;
}

.table-body {
  max-height: 300px;
  overflow-y: auto;
}

.table-row {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  padding: 0.6rem 0.8rem;
  border-bottom: 1px solid var(--border-color);
  background-color: white;
}

.table-row:nth-child(even) {
  background-color: rgba(0, 0, 0, 0.02);
}

.table-row:last-child {
  border-bottom: none;
}

.outcome-cell {
  font-weight: 600;
}

.percent-cell {
  color: var(--primary-color);
  font-weight: 600;
}

/* 图表分析 */
.chart-analysis h3 {
  margin-bottom: 1rem;
}

.chart {
  display: flex;
  flex-direction: column;
  gap: 0.8rem;
  margin-bottom: 1.5rem;
}

.bar-item {
  display: flex;
  align-items: center;
  gap: 1rem;
}

.bar-label {
  width: 60px;
  text-align: right;
  font-weight: 600;
  font-size: 0.9rem;
  color: var(--text-color);
}

.bar-container {
  flex: 1;
  background-color: rgba(0, 0, 0, 0.05);
  height: 30px;
  border-radius: var(--radius-sm);
  overflow: hidden;
}

.bar {
  height: 100%;
  color: white;
  display: flex;
  align-items: center;
  padding: 0 0.8rem;
  font-size: 0.9rem;
  font-weight: 600;
  transition: width 0.8s cubic-bezier(0.22, 1, 0.36, 1);
}

/* 条形图颜色 */
.bar-primary {
  background-color: var(--primary-color);
}

.bar-secondary {
  background-color: var(--secondary-color);
}

.bar-accent {
  background-color: var(--accent-color);
}

.bar-success {
  background-color: var(--success-color);
}

.bar-warning {
  background-color: var(--warning-color);
}

.bar-danger {
  background-color: var(--danger-color);
}

.chart-info {
  display: flex;
  justify-content: space-around;
  background-color: rgba(0, 0, 0, 0.03);
  padding: 1rem;
  border-radius: var(--radius-sm);
  margin-top: 1rem;
}

.chart-info p {
  margin: 0;
}

/* 无结果提示 */
.no-results {
  padding: 3rem 1rem;
  text-align: center;
  color: var(--text-light);
  font-style: italic;
}

/* 响应式调整 */
@media (max-width: 768px) {
  .controls-panel {
    flex-direction: column;
    align-items: stretch;
  }

  .start-button {
    margin-left: 0;
    width: 100%;
    justify-content: center;
  }

  .bar-label {
    width: 40px;
    font-size: 0.8rem;
  }
}
</style>
