<template>
  <div class="probability-calculator card">
    <div class="calculator-header">
      <h2>
        <span class="icon">📊</span>
        核心概率概念与公式计算器
      </h2>
      <p class="calculator-description">探索概率论的基本概念和计算方法</p>
    </div>

    <div class="calculator-content">
      <div class="content-tabs">
        <button
          class="tab-button"
          :class="{ 'active': activeTab === 'concepts' }"
          @click="activeTab = 'concepts'"
        >
          <span class="tab-icon">📚</span>
          概率概念
        </button>
        <button
          class="tab-button"
          :class="{ 'active': activeTab === 'calculator' }"
          @click="activeTab = 'calculator'"
        >
          <span class="tab-icon">🧮</span>
          概率计算器
        </button>
      </div>

      <div class="tab-content">
        <!-- 概率概念标签页 -->
        <div v-if="activeTab === 'concepts'" class="concepts-section">
          <div class="concepts-grid">
            <div
              class="concept-card"
              v-for="(concept, index) in probabilityConcepts"
              :key="concept.name"
              :class="{ 'expanded': expandedConcept === index }"
              @click="toggleConcept(index)"
            >
              <div class="concept-header">
                <h4>{{ concept.name }}</h4>
                <span class="expand-icon">{{ expandedConcept === index ? '−' : '+' }}</span>
              </div>
              <div class="concept-body">
                <p v-html="concept.description"></p>
                <div v-if="concept.example" class="example">
                  <strong>示例:</strong> {{ concept.example }}
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- 计算器标签页 -->
        <div v-if="activeTab === 'calculator'" class="calculator-section">
          <div class="calculator-types">
            <button
              class="calc-type-button"
              :class="{ 'active': activeCalc === 'independent' }"
              @click="activeCalc = 'independent'"
            >
              独立事件
            </button>
            <button
              class="calc-type-button"
              :class="{ 'active': activeCalc === 'conditional' }"
              @click="activeCalc = 'conditional'"
            >
              条件概率
            </button>
          </div>

          <!-- 独立事件计算器 -->
          <div v-if="activeCalc === 'independent'" class="calculator-form">
            <div class="form-header">
              <h4>独立事件概率计算</h4>
              <p class="form-description">计算两个独立事件的联合概率和并集概率</p>
            </div>

            <div class="input-groups">
              <div class="input-group">
                <label for="probA">P(A):</label>
                <input
                  type="number"
                  id="probA"
                  v-model.number="probA"
                  min="0"
                  max="1"
                  step="0.01"
                  placeholder="0-1之间"
                />
              </div>
              <div class="input-group">
                <label for="probB">P(B):</label>
                <input
                  type="number"
                  id="probB"
                  v-model.number="probB"
                  min="0"
                  max="1"
                  step="0.01"
                  placeholder="0-1之间"
                />
              </div>
            </div>

            <div v-if="isValidInput" class="results-panel">
              <div class="result-item">
                <div class="result-formula">
                  P(A ∩ B) = P(A) × P(B)
                </div>
                <div class="result-value">
                  {{ (probA * probB).toFixed(4) }}
                </div>
              </div>
              <div class="result-item">
                <div class="result-formula">
                  P(A ∪ B) = P(A) + P(B) - P(A ∩ B)
                </div>
                <div class="result-value">
                  {{ (probA + probB - probA * probB).toFixed(4) }}
                </div>
              </div>
            </div>
            <p v-else class="tip">请输入有效的 P(A) 和 P(B) (0 到 1 之间)</p>
          </div>

          <!-- 条件概率计算器 -->
          <div v-if="activeCalc === 'conditional'" class="calculator-form">
            <div class="form-header">
              <h4>条件概率计算</h4>
              <p class="form-description">计算在事件B发生的条件下，事件A发生的概率</p>
            </div>

            <div class="input-groups">
              <div class="input-group">
                <label for="probAandB">P(A∩B):</label>
                <input
                  type="number"
                  id="probAandB"
                  v-model.number="probAandB"
                  min="0"
                  max="1"
                  step="0.01"
                  placeholder="A和B同时发生的概率"
                />
              </div>
              <div class="input-group">
                <label for="probBCond">P(B):</label>
                <input
                  type="number"
                  id="probBCond"
                  v-model.number="probBCond"
                  min="0"
                  max="1"
                  step="0.01"
                  placeholder="B发生的概率"
                />
              </div>
            </div>

            <div v-if="isValidConditionalInput" class="results-panel">
              <div class="result-item">
                <div class="result-formula">
                  P(A|B) = P(A∩B) / P(B)
                </div>
                <div class="result-value">
                  {{ (probAandB / probBCond).toFixed(4) }}
                </div>
              </div>
            </div>
            <p v-else-if="probBCond === 0" class="tip error">P(B) 不能为 0</p>
            <p v-else class="tip">请输入有效的概率值 (0 到 1 之间)</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';

// 接口定义
interface ProbabilityConcept {
  name: string;
  description: string;
  example?: string;
}

// 状态管理
const activeTab = ref<'concepts' | 'calculator'>('concepts');
const activeCalc = ref<'independent' | 'conditional'>('independent');
const expandedConcept = ref<number | null>(null);

// 独立事件计算器状态
const probA = ref<number | null>(0.5);
const probB = ref<number | null>(0.5);

// 条件概率计算器状态
const probAandB = ref<number | null>(0.3);
const probBCond = ref<number | null>(0.6);

// 计算属性
const isValidInput = computed(() => {
  return probA.value !== null &&
         probB.value !== null &&
         probA.value >= 0 &&
         probA.value <= 1 &&
         probB.value >= 0 &&
         probB.value <= 1;
});

const isValidConditionalInput = computed(() => {
  return probAandB.value !== null &&
         probBCond.value !== null &&
         probAandB.value >= 0 &&
         probAandB.value <= 1 &&
         probBCond.value > 0 &&
         probBCond.value <= 1 &&
         probAandB.value <= probBCond.value;
});

// 方法
const toggleConcept = (index: number) => {
  if (expandedConcept.value === index) {
    expandedConcept.value = null;
  } else {
    expandedConcept.value = index;
  }
};

// 概率概念数据
const probabilityConcepts = ref<ProbabilityConcept[]>([
  {
    name: '随机事件 (Random Event)',
    description: '在随机试验中可能发生也可能不发生，而在大量重复试验中具有某种统计规律性的事件。',
    example: '抛一枚硬币，出现"正面"是一个随机事件。'
  },
  {
    name: '样本空间 (Sample Space)',
    description: '随机试验所有可能结果组成的集合，通常用 S 或 Ω 表示。样本空间中的每个元素称为一个样本点。',
    example: '掷一个六面骰子，样本空间 S = {1, 2, 3, 4, 5, 6}。'
  },
  {
    name: '概率 (Probability)',
    description: '度量一个事件发生可能性大小的数值，介于 0 和 1 之间。概率为 0 表示事件不可能发生，概率为 1 表示事件必然发生。',
    example: '在公平的抛硬币试验中，出现"正面"的概率是 0.5。'
  },
  {
    name: '概率公理 (Axioms of Probability)',
    description: `
      <ul>
        <li><strong>非负性:</strong> 对于任何事件 A，P(A) ≥ 0。</li>
        <li><strong>规范性:</strong> 对于样本空间 S，P(S) = 1。</li>
        <li><strong>可列可加性:</strong> 若 A<sub>1</sub>, A<sub>2</sub>, ... 是两两互不相容的事件（即 A<sub>i</sub> ∩ A<sub>j</sub> = ∅，当 i ≠ j），则 P(A<sub>1</sub> ∪ A<sub>2</sub> ∪ ...) = P(A<sub>1</sub>) + P(A<sub>2</sub>) + ...</li>
      </ul>
    `,
  },
  {
    name: '条件概率 (Conditional Probability)',
    description: '在事件 B 已经发生的条件下，事件 A 发生的概率，记为 P(A|B)。计算公式为: P(A|B) = P(A∩B) / P(B)，其中 P(B) > 0。',
    example: '一个盒子里有3个红球和2个蓝球。不放回地抽取两次。已知第一次抽到红球，那么第二次仍然抽到红球的概率是 P(红|红) = 2/4 = 0.5。'
  },
  {
    name: '事件的独立性 (Independence of Events)',
    description: '如果事件 A 的发生不影响事件 B 发生的概率（反之亦然），则称事件 A 和 B 相互独立。数学上表示为 P(A∩B) = P(A) * P(B)。',
    example: '连续抛两次硬币，第一次出现"正面"和第二次出现"正面"是相互独立的事件。'
  },
  {
    name: '贝叶斯定理 (Bayes\' Theorem)',
    description: '用于计算在已知事件 B 发生的条件下，事件 A 发生的概率。公式为: P(A|B) = [P(B|A) × P(A)] / P(B)。',
    example: '医学检测中，已知某疾病在人群中的发病率为1%，检测的灵敏度为90%（患病者检测呈阳性的概率），特异度为95%（健康者检测呈阴性的概率）。当一个人检测呈阳性时，他真正患病的概率约为15.4%。'
  }
]);
</script>

<style scoped>
/* 主容器样式 */
.probability-calculator {
  padding: 0;
  border: none;
  border-radius: var(--radius-lg);
  background-color: var(--bg-card);
  box-shadow: var(--shadow-md);
  overflow: hidden;
  transition: all var(--transition-normal);
}

.probability-calculator:hover {
  box-shadow: var(--shadow-lg);
  transform: translateY(-5px);
}

/* 标题区域 */
.calculator-header {
  padding: 1.5rem;
  background-image: linear-gradient(135deg, var(--secondary-color), var(--accent-color));
  color: white;
  text-align: center;
}

.calculator-header h2 {
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

.calculator-description {
  margin: 0.5rem 0 0;
  opacity: 0.9;
  font-size: 0.95rem;
}

/* 内容区域 */
.calculator-content {
  padding: 0;
}

/* 标签页导航 */
.content-tabs {
  display: flex;
  border-bottom: 1px solid var(--border-color);
}

.tab-button {
  flex: 1;
  padding: 1rem;
  background: none;
  border: none;
  color: var(--text-light);
  font-weight: 600;
  cursor: pointer;
  transition: all var(--transition-fast);
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
}

.tab-button:hover {
  background-color: rgba(0, 0, 0, 0.03);
  color: var(--secondary-color);
}

.tab-button.active {
  color: var(--secondary-color);
  border-bottom: 3px solid var(--secondary-color);
}

.tab-icon {
  font-size: 1.2rem;
}

/* 标签页内容 */
.tab-content {
  padding: 1.5rem;
  min-height: 400px;
}

/* 概念卡片网格 */
.concepts-grid {
  display: grid;
  gap: 1rem;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
}

.concept-card {
  background-color: white;
  border-radius: var(--radius-sm);
  box-shadow: var(--shadow-sm);
  overflow: hidden;
  cursor: pointer;
  transition: all var(--transition-fast);
  border: 1px solid var(--border-color);
}

.concept-card:hover {
  box-shadow: var(--shadow-md);
  transform: translateY(-3px);
}

.concept-header {
  padding: 1rem;
  background-color: rgba(0, 0, 0, 0.02);
  border-bottom: 1px solid var(--border-color);
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.concept-header h4 {
  margin: 0;
  color: var(--secondary-color);
  font-size: 1rem;
}

.expand-icon {
  font-size: 1.2rem;
  color: var(--text-light);
  font-weight: bold;
}

.concept-body {
  padding: 0;
  max-height: 0;
  overflow: hidden;
  transition: all var(--transition-normal);
}

.concept-card.expanded .concept-body {
  padding: 1rem;
  max-height: 500px;
}

.concept-body p {
  margin-top: 0;
  color: var(--text-color);
  line-height: 1.6;
}

.example {
  margin-top: 0.8rem;
  padding: 0.8rem;
  background-color: rgba(0, 0, 0, 0.03);
  border-left: 3px solid var(--secondary-color);
  border-radius: 0 var(--radius-sm) var(--radius-sm) 0;
  font-size: 0.9rem;
}

/* 计算器部分 */
.calculator-section {
  padding: 0;
}

.calculator-types {
  display: flex;
  margin-bottom: 1.5rem;
  background-color: rgba(0, 0, 0, 0.03);
  border-radius: var(--radius-sm);
  padding: 0.3rem;
}

.calc-type-button {
  flex: 1;
  padding: 0.8rem;
  background: none;
  border: none;
  border-radius: var(--radius-sm);
  color: var(--text-light);
  font-weight: 600;
  cursor: pointer;
  transition: all var(--transition-fast);
}

.calc-type-button:hover {
  color: var(--secondary-color);
}

.calc-type-button.active {
  background-color: white;
  color: var(--secondary-color);
  box-shadow: var(--shadow-sm);
}

.calculator-form {
  background-color: white;
  border-radius: var(--radius-sm);
  padding: 1.5rem;
  box-shadow: var(--shadow-sm);
}

.form-header {
  margin-bottom: 1.5rem;
}

.form-header h4 {
  margin: 0 0 0.5rem 0;
  color: var(--secondary-color);
}

.form-description {
  margin: 0;
  color: var(--text-light);
  font-size: 0.9rem;
}

.input-groups {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 1rem;
  margin-bottom: 1.5rem;
}

.input-group {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.input-group label {
  font-weight: 600;
  color: var(--text-color);
  font-size: 0.9rem;
}

.input-group input {
  padding: 0.8rem;
  border: 1px solid var(--border-color);
  border-radius: var(--radius-sm);
  font-size: 1rem;
  transition: all var(--transition-fast);
}

.input-group input:focus {
  border-color: var(--secondary-color);
  box-shadow: 0 0 0 3px rgba(131, 56, 236, 0.2);
}

.results-panel {
  background-color: rgba(0, 0, 0, 0.02);
  border-radius: var(--radius-sm);
  padding: 1rem;
}

.result-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.8rem;
  border-bottom: 1px dashed var(--border-color);
}

.result-item:last-child {
  border-bottom: none;
}

.result-formula {
  font-weight: 600;
  color: var(--text-color);
}

.result-value {
  font-size: 1.2rem;
  font-weight: 700;
  color: var(--secondary-color);
  background-color: white;
  padding: 0.3rem 0.8rem;
  border-radius: var(--radius-sm);
  box-shadow: var(--shadow-sm);
}

.tip {
  margin: 1rem 0 0;
  padding: 0.8rem;
  background-color: rgba(0, 0, 0, 0.03);
  border-radius: var(--radius-sm);
  font-style: italic;
  color: var(--text-light);
}

.tip.error {
  background-color: rgba(239, 71, 111, 0.1);
  color: var(--danger-color);
}

/* 响应式调整 */
@media (max-width: 768px) {
  .concepts-grid {
    grid-template-columns: 1fr;
  }

  .input-groups {
    grid-template-columns: 1fr;
  }

  .result-item {
    flex-direction: column;
    align-items: flex-start;
    gap: 0.5rem;
  }

  .result-value {
    align-self: flex-end;
  }
}
</style>
