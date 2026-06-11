# 线性方程组求解器 实现计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 构建一个移动端友好的单页面线性方程组求解器，用户输入增广矩阵，输出解向量（优先分数表示）。

**Architecture:** 单文件 `index.html`，内嵌 CSS + JS。JS 分三层：`Fraction`（有理数运算 + GCD 约分）→ `solve()`（高斯消元 + 列主元）→ UI 控制器（DOM 事件、网格渲染、结果展示）。

**Tech Stack:** 纯 HTML + CSS + JavaScript，零外部依赖。

---

## 文件结构

| 文件 | 职责 |
|------|------|
| `index.html` | 全部 HTML 结构、CSS 样式、JS 逻辑 |

`index.html` 内部 JS 模块划分：

| 模块 | 职责 |
|------|------|
| `Fraction` class | 有理数表示（num/den），加减乘除，GCD 约分，格式化输出 |
| `parseNumber(str)` | 解析输入字符串为 Fraction（支持整数、分数、小数） |
| `gaussElimination(matrix)` | 高斯消元，返回解向量或错误类型 |
| UI 函数 | 渲染输入网格、切换阶数、读取输入、展示结果 |

---

### Task 1: HTML 骨架和移动端基础样式

**文件:**
- 创建: `index.html`

- [ ] **Step 1: 建立 HTML 骨架**

写入包含完整 `<head>` 和 `<body>` 结构的 HTML 文件：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>线性方程组求解器</title>
  <style>
    /* CSS 占位，后续步骤填充 */
  </style>
</head>
<body>
  <div class="container">
    <header>
      <h1>📐 线性方程组求解器</h1>
    </header>
    <main>
      <section class="order-section">
        <label for="order-select">矩阵阶数：</label>
        <select id="order-select">
          <option value="2">2</option>
          <option value="3" selected>3</option>
          <option value="4">4</option>
          <option value="5">5</option>
          <option value="6">6</option>
          <option value="7">7</option>
          <option value="8">8</option>
          <option value="9">9</option>
          <option value="10">10</option>
        </select>
      </section>
      <section class="matrix-section">
        <h2>增广矩阵输入</h2>
        <p class="hint">支持整数 <code>2</code>、分数 <code>1/2</code>、小数 <code>0.5</code></p>
        <div id="matrix-container"></div>
        <div class="button-row">
          <button id="solve-btn">求解</button>
          <button id="clear-btn">清空</button>
        </div>
      </section>
      <section class="result-section">
        <h2>结果</h2>
        <div id="result-display"></div>
      </section>
    </main>
  </div>
  <script>
    // JS 占位，后续步骤填充
  </script>
</body>
</html>
```

- [ ] **Step 2: 添加移动端基础 CSS**

在 `<style>` 标签内添加基础样式：

```css
*, *::before, *::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
  background: #f5f5f5;
  color: #333;
  line-height: 1.5;
  -webkit-text-size-adjust: 100%;
}

.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 16px;
}

header {
  text-align: center;
  padding: 16px 0;
}

h1 {
  font-size: 1.4rem;
  font-weight: 600;
}

h2 {
  font-size: 1.1rem;
  font-weight: 500;
  margin: 12px 0 8px;
}

section {
  background: #fff;
  border-radius: 12px;
  padding: 16px;
  margin-bottom: 12px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.08);
}

.order-section {
  display: flex;
  align-items: center;
  gap: 10px;
}

.order-section label {
  font-weight: 500;
  white-space: nowrap;
}

.order-section select {
  font-size: 1rem;
  padding: 8px 12px;
  border-radius: 8px;
  border: 1px solid #ccc;
  background: #fff;
  min-width: 64px;
}

.hint {
  font-size: 0.85rem;
  color: #888;
  margin-bottom: 10px;
}

.button-row {
  display: flex;
  gap: 10px;
  margin-top: 12px;
}

button {
  font-size: 1rem;
  padding: 10px 24px;
  border-radius: 8px;
  border: none;
  cursor: pointer;
  font-weight: 500;
  min-height: 44px;
  min-width: 80px;
  touch-action: manipulation;
}

#solve-btn {
  background: #2563eb;
  color: #fff;
  flex: 1;
}

#clear-btn {
  background: #e5e7eb;
  color: #333;
  flex: 1;
}

#result-display {
  min-height: 40px;
  font-size: 1.05rem;
  line-height: 1.8;
}
```

- [ ] **Step 3: 验证 HTML 在浏览器中打开**

用浏览器打开 `index.html`，确认：
- 页面标题和阶数下拉菜单可见
- 下拉菜单默认选中 3
- 两个按钮可见
- 在手机宽度（375px）下布局正常，无横向溢出

- [ ] **Step 4: 提交**

```bash
git add index.html
git commit -m "feat: add HTML skeleton and mobile base styles"
```

---

### Task 2: 矩阵输入网格

**文件:**
- 修改: `index.html`

- [ ] **Step 1: 添加网格 CSS**

在 `<style>` 标签的按钮样式后追加：

```css
/* 矩阵网格 */
.matrix-grid {
  display: grid;
  gap: 6px;
  margin: 8px 0;
}

.matrix-grid input {
  width: 100%;
  aspect-ratio: 1.5;
  min-height: 44px;
  text-align: center;
  font-size: 1rem;
  border: 1px solid #d1d5db;
  border-radius: 8px;
  padding: 4px;
  -webkit-appearance: none;
  appearance: none;
}

.matrix-grid input:focus {
  outline: 2px solid #2563eb;
  outline-offset: -1px;
}

/* 常数项列背景色 */
.matrix-grid .augmented-col {
  background: #fef3c7;
  border-color: #f59e0b;
}

.result-matrix-row {
  display: flex;
  gap: 24px;
  flex-wrap: wrap;
}

.result-matrix {
  flex: 1;
  min-width: 120px;
}

.result-matrix h3 {
  font-size: 0.9rem;
  font-weight: 500;
  margin-bottom: 8px;
  color: #666;
}

.result-grid {
  display: grid;
  gap: 4px;
  border-collapse: collapse;
}

.result-grid .cell {
  text-align: center;
  padding: 4px 8px;
  border: 1px solid #e5e7eb;
  border-radius: 4px;
  font-size: 0.9rem;
  background: #fafafa;
}
```

- [ ] **Step 2: 添加渲染函数**

在 `<script>` 标签内添加：

```javascript
const matrixContainer = document.getElementById('matrix-container');
const orderSelect = document.getElementById('order-select');
const resultDisplay = document.getElementById('result-display');

function renderMatrixGrid(n) {
  matrixContainer.innerHTML = '';
  const grid = document.createElement('div');
  grid.className = 'matrix-grid';
  // n 行, n+1 列
  grid.style.gridTemplateColumns = `repeat(${n + 1}, 1fr)`;

  for (let row = 0; row < n; row++) {
    for (let col = 0; col < n + 1; col++) {
      const input = document.createElement('input');
      input.type = 'text';
      input.inputMode = 'decimal';
      input.placeholder = '0';
      input.dataset.row = row;
      input.dataset.col = col;
      if (col === n) {
        input.classList.add('augmented-col');
      }
      grid.appendChild(input);
    }
  }

  matrixContainer.appendChild(grid);
}

// 初始渲染
renderMatrixGrid(3);

// 阶数切换
orderSelect.addEventListener('change', () => {
  const n = parseInt(orderSelect.value);
  renderMatrixGrid(n);
});
```

- [ ] **Step 3: 验证网格**

浏览器打开 `index.html`：
- 默认 3×4 网格可见（3 行 4 列，最后一列黄色背景）
- 切换阶数为 5，网格变为 5×6
- 切换阶数为 2，网格变为 2×3
- 手机宽度下格子不溢出

- [ ] **Step 4: 提交**

```bash
git add index.html
git commit -m "feat: add matrix input grid"
```

---

### Task 3: Fraction 分数类

**文件:**
- 修改: `index.html`

- [ ] **Step 1: 实现 Fraction 类**

在 `<script>` 标签内、`renderMatrixGrid` 之前插入：

```javascript
// ── Fraction 有理数类 ──
class Fraction {
  constructor(num, den = 1) {
    if (den < 0) { num = -num; den = -den; }
    if (num === 0) { den = 1; }
    const g = Fraction.gcd(Math.abs(num), Math.abs(den));
    this.num = num / g;
    this.den = den / g;
  }

  static gcd(a, b) {
    while (b !== 0) { [a, b] = [b, a % b]; }
    return a;
  }

  add(other) {
    const o = Fraction.from(other);
    return new Fraction(this.num * o.den + o.num * this.den, this.den * o.den);
  }

  sub(other) {
    const o = Fraction.from(other);
    return new Fraction(this.num * o.den - o.num * this.den, this.den * o.den);
  }

  mul(other) {
    const o = Fraction.from(other);
    return new Fraction(this.num * o.num, this.den * o.den);
  }

  div(other) {
    const o = Fraction.from(other);
    if (o.num === 0) throw new Error('Division by zero');
    return new Fraction(this.num * o.den, this.den * o.num);
  }

  neg() {
    return new Fraction(-this.num, this.den);
  }

  isZero() {
    return this.num === 0;
  }

  abs() {
    return new Fraction(Math.abs(this.num), this.den);
  }

  toString() {
    if (this.den === 1) return `${this.num}`;
    return `${this.num}/${this.den}`;
  }

  toDecimal(precision = 6) {
    const val = this.num / this.den;
    return parseFloat(val.toPrecision(precision));
  }

  static from(val) {
    if (val instanceof Fraction) return val;
    if (typeof val === 'number') return Fraction.fromDecimal(val);
    if (typeof val === 'string') return parseInput(val);
    throw new Error(`Cannot convert to Fraction: ${val}`);
  }

  static fromDecimal(dec) {
    if (Number.isInteger(dec)) return new Fraction(dec);
    // 分数化：找到分母
    const s = dec.toString();
    const parts = s.split('.');
    if (parts.length === 1) return new Fraction(dec);
    const decimalPlaces = parts[1].length;
    const den = Math.pow(10, decimalPlaces);
    const num = Math.round(dec * den);
    return new Fraction(num, den);
  }
}

// ── 输入解析 ──
function parseInput(str) {
  str = str.trim();
  if (str === '') return new Fraction(0);

  // 分数格式 "a/b"
  if (str.includes('/')) {
    const parts = str.split('/');
    if (parts.length !== 2) return new Fraction(0);
    const num = parseFloat(parts[0]);
    const den = parseFloat(parts[1]);
    if (isNaN(num) || isNaN(den) || den === 0) return new Fraction(0);
    if (Number.isInteger(num) && Number.isInteger(den)) {
      return new Fraction(num, den);
    }
    return new Fraction(num).div(new Fraction(den));
  }

  // 小数或整数
  const val = parseFloat(str);
  if (isNaN(val)) return new Fraction(0);
  return Fraction.fromDecimal(val);
}
```

- [ ] **Step 2: 打开浏览器控制台测试 Fraction**

在 Chrome DevTools 控制台运行以下测试（手动验证）：

```javascript
// Test 1: 基本运算
const a = new Fraction(1, 2);
const b = new Fraction(1, 3);
console.log(a.add(b).toString());    // 期望: "5/6"
console.log(a.mul(b).toString());    // 期望: "1/6"
console.log(a.div(b).toString());    // 期望: "3/2"

// Test 2: 解析
console.log(parseInput('0.5').toString());  // 期望: "1/2"
console.log(parseInput('1/4').toString());  // 期望: "1/4"
console.log(parseInput('3').toString());    // 期望: "3"
console.log(parseInput('0').toString());    // 期望: "0"

// Test 3: 约分
const c = new Fraction(6, 8);
console.log(c.toString());  // 期望: "3/4"
```

- [ ] **Step 3: 提交**

```bash
git add index.html
git commit -m "feat: add Fraction class with GCD reduction"
```

---

### Task 4: 高斯消元求解器

**文件:**
- 修改: `index.html`

- [ ] **Step 1: 实现高斯消元函数**

在 `<script>` 标签内 Fraction 类之后插入：

```javascript
// ── 高斯消元（列主元）──
function gaussElimination(augMatrix) {
  // augMatrix: n × (n+1) 的 Fraction[][]
  const n = augMatrix.length;
  const m = [...augMatrix.map(row => [...row])]; // 深拷贝

  // 前向消元
  for (let col = 0; col < n; col++) {
    // 列主元选取
    let maxRow = col;
    let maxVal = m[col][col].abs();
    for (let row = col + 1; row < n; row++) {
      const val = m[row][col].abs();
      if (val.num > maxVal.num || (val.num === maxVal.num && val.den < maxVal.den)) {
        maxVal = val;
        maxRow = row;
      }
    }

    // 奇异矩阵检查
    if (m[maxRow][col].isZero()) {
      // 判断无解 vs 无穷多解
      const rankA = col; // 当前列之前都是线性无关的
      // 检查增广列
      let augmentedRank = rankA;
      for (let row = col; row < n; row++) {
        if (!m[row][n].isZero()) {
          augmentedRank = rankA + 1;
          break;
        }
      }
      if (augmentedRank > rankA) return { type: 'no-solution' };
      return { type: 'infinite-solutions' };
    }

    // 交换行
    if (maxRow !== col) {
      [m[col], m[maxRow]] = [m[maxRow], m[col]];
    }

    // 归一化主元行
    const pivot = m[col][col];
    for (let j = col; j <= n; j++) {
      m[col][j] = m[col][j].div(pivot);
    }

    // 消去下方行
    for (let row = col + 1; row < n; row++) {
      const factor = m[row][col];
      if (factor.isZero()) continue;
      for (let j = col; j <= n; j++) {
        m[row][j] = m[row][j].sub(factor.mul(m[col][j]));
      }
    }
  }

  // 回代
  const x = new Array(n).fill(null);
  for (let i = n - 1; i >= 0; i--) {
    let sum = new Fraction(0);
    for (let j = i + 1; j < n; j++) {
      sum = sum.add(m[i][j].mul(x[j]));
    }
    x[i] = m[i][n].sub(sum);
  }

  return { type: 'unique', solution: x };
}
```

- [ ] **Step 2: 手动测试消元**

在浏览器控制台（在页面加载后）测试：

```javascript
// 方程组:
// x + y = 3
// x - y = 1
// 增广矩阵: [[1,1,3],[1,-1,1]]
const mat = [
  [new Fraction(1), new Fraction(1), new Fraction(3)],
  [new Fraction(1), new Fraction(-1), new Fraction(1)]
];
const result = gaussElimination(mat);
console.log(result.type);                      // "unique"
console.log(result.solution.map(x => x.toString())); // ["2", "1"]

// 无解: x+y=1, x+y=2
const mat2 = [
  [new Fraction(1), new Fraction(1), new Fraction(1)],
  [new Fraction(1), new Fraction(1), new Fraction(2)]
];
console.log(gaussElimination(mat2).type); // "no-solution"
```

- [ ] **Step 3: 提交**

```bash
git add index.html
git commit -m "feat: add Gaussian elimination solver"
```

---

### Task 5: UI 交互和结果展示

**文件:**
- 修改: `index.html`

- [ ] **Step 1: 实现读取输入和展示结果的 UI 逻辑**

在 `</script>` 之前追加（在 `orderSelect` 事件监听器之后）：

```javascript
// ── UI 交互 ──
const solveBtn = document.getElementById('solve-btn');
const clearBtn = document.getElementById('clear-btn');

// 读取增广矩阵
function readAugmentedMatrix() {
  const inputs = matrixContainer.querySelectorAll('input');
  const n = parseInt(orderSelect.value);
  const matrix = [];
  for (let row = 0; row < n; row++) {
    matrix[row] = [];
    for (let col = 0; col < n + 1; col++) {
      const input = matrixContainer.querySelector(`input[data-row="${row}"][data-col="${col}"]`);
      matrix[row][col] = parseInput(input ? input.value : '0');
    }
  }
  return matrix;
}

// 渲染系数矩阵和增广矩阵（只读）
function renderResultMatrices(augMatrix) {
  const n = augMatrix.length;
  const existingDisplay = document.querySelector('.result-matrices');
  if (existingDisplay) existingDisplay.remove();

  const container = document.createElement('div');
  container.className = 'result-matrices result-matrix-row';

  // 系数矩阵 A
  const aDiv = document.createElement('div');
  aDiv.className = 'result-matrix';
  aDiv.innerHTML = '<h3>系数矩阵 A</h3>';
  const aGrid = document.createElement('div');
  aGrid.className = 'result-grid';
  aGrid.style.gridTemplateColumns = `repeat(${n}, 1fr)`;
  for (let r = 0; r < n; r++) {
    for (let c = 0; c < n; c++) {
      const cell = document.createElement('div');
      cell.className = 'cell';
      cell.textContent = augMatrix[r][c].toString();
      aGrid.appendChild(cell);
    }
  }
  aDiv.appendChild(aGrid);
  container.appendChild(aDiv);

  // 增广矩阵 [A|b]
  const augDiv = document.createElement('div');
  augDiv.className = 'result-matrix';
  augDiv.innerHTML = '<h3>增广矩阵 [A|b]</h3>';
  const augGrid = document.createElement('div');
  augGrid.className = 'result-grid';
  augGrid.style.gridTemplateColumns = `repeat(${n + 1}, 1fr)`;
  for (let r = 0; r < n; r++) {
    for (let c = 0; c < n + 1; c++) {
      const cell = document.createElement('div');
      cell.className = 'cell';
      if (c === n) cell.style.background = '#fef3c7';
      cell.textContent = augMatrix[r][c].toString();
      augGrid.appendChild(cell);
    }
  }
  augDiv.appendChild(augGrid);
  container.appendChild(augDiv);

  resultDisplay.parentNode.insertBefore(container, resultDisplay);
}

// 求解
solveBtn.addEventListener('click', () => {
  const augMatrix = readAugmentedMatrix();
  const result = gaussElimination(augMatrix);

  renderResultMatrices(augMatrix);

  if (result.type === 'unique') {
    const parts = result.solution.map((x, i) => {
      let formatted = x.toString();
      return `x<sub>${i + 1}</sub> = ${formatted}`;
    });
    resultDisplay.innerHTML = parts.join('<br>');
    resultDisplay.style.color = '#333';
  } else if (result.type === 'no-solution') {
    resultDisplay.innerHTML = '⚠️ 方程组无解';
    resultDisplay.style.color = '#dc2626';
  } else if (result.type === 'infinite-solutions') {
    resultDisplay.innerHTML = '⚠️ 方程组有无穷多解';
    resultDisplay.style.color = '#e67e00';
  }
});

// 清空
clearBtn.addEventListener('click', () => {
  const inputs = matrixContainer.querySelectorAll('input');
  inputs.forEach(input => { input.value = ''; });
  const existingDisplay = document.querySelector('.result-matrices');
  if (existingDisplay) existingDisplay.remove();
  resultDisplay.innerHTML = '';
});
```

- [ ] **Step 2: 端到端验证**

浏览器打开 `index.html`：
1. 默认 3 阶网格中填入简单方程组：
   ```
   1  1  1 | 6
   0  1  1 | 3
   0  0  1 | 1
   ```
2. 点击"求解"
3. 验证结果：x₁=3, x₂=2, x₃=1
4. 验证系数矩阵和增广矩阵展示正确
5. 点击"清空"验证所有输入和结果清除

- [ ] **Step 3: 测试无解和无穷多解**

无解测试（填入）：
```
1  1 | 1
1  1 | 2
```
→ 期望显示 "⚠️ 方程组无解"

无穷多解测试（填入）：
```
1  1 | 1
2  2 | 2
```
→ 期望显示 "⚠️ 方程组有无穷多解"

- [ ] **Step 4: 提交**

```bash
git add index.html
git commit -m "feat: wire up UI interactions and result display"
```

---

### Task 6: 移动端优化和最终打磨

**文件:**
- 修改: `index.html`

- [ ] **Step 1: 添加移动端专属 CSS 优化**

在 `</style>` 之前追加：

```css
/* 移动端优化 */
@media (max-width: 480px) {
  .container {
    padding: 10px;
  }

  h1 {
    font-size: 1.2rem;
  }

  section {
    padding: 12px;
    border-radius: 10px;
  }

  .matrix-grid {
    gap: 4px;
  }

  .matrix-grid input {
    font-size: 0.85rem;
    min-height: 40px;
    padding: 2px;
    border-radius: 6px;
  }

  .result-matrix-row {
    flex-direction: column;
    gap: 12px;
  }
}

/* 小屏手机 (320px) 进一步缩小 */
@media (max-width: 360px) {
  .matrix-grid input {
    font-size: 0.75rem;
    min-height: 36px;
  }

  .container {
    padding: 6px;
  }

  section {
    padding: 8px;
  }

  button {
    padding: 8px 16px;
    font-size: 0.9rem;
  }
}

/* 横屏/大阶数兜底 */
.matrix-scroll {
  overflow-x: auto;
  -webkit-overflow-scrolling: touch;
}

/* 避免 iOS 缩放输入框 */
input[type="text"] {
  font-size: 16px;
}
```

- [ ] **Step 2: 给矩阵容器加横向滚动保护**

修改 `renderMatrixGrid` 函数中 `matrixContainer` 的操作，在渲染网格之前包裹滚动容器：

```javascript
function renderMatrixGrid(n) {
  matrixContainer.innerHTML = '';
  const scrollWrapper = document.createElement('div');
  scrollWrapper.className = 'matrix-scroll';

  const grid = document.createElement('div');
  grid.className = 'matrix-grid';
  grid.style.gridTemplateColumns = `repeat(${n + 1}, 1fr)`;

  for (let row = 0; row < n; row++) {
    for (let col = 0; col < n + 1; col++) {
      const input = document.createElement('input');
      input.type = 'text';
      input.inputMode = 'decimal';
      input.placeholder = '0';
      input.dataset.row = row;
      input.dataset.col = col;
      if (col === n) {
        input.classList.add('augmented-col');
      }
      grid.appendChild(input);
    }
  }

  scrollWrapper.appendChild(grid);
  matrixContainer.appendChild(scrollWrapper);
}
```

- [ ] **Step 3: 在各种手机宽度下验证**

使用 Chrome DevTools 设备模拟，验证以下宽度：
- iPhone SE (375px) — 3 阶正常，10 阶可横向滚动
- iPhone 12 Pro (390px) — 同上
- Samsung Galaxy S20 (360px) — 同上
- iPad (768px) — 全部正常显示

- [ ] **Step 4: 提交**

```bash
git add index.html
git commit -m "style: mobile-responsive polish"
```

---

### Task 7: 最终验证

**文件:**
- 修改: 无（纯验证）

- [ ] **Step 1: 测试边界情况**

确认以下场景均已通过（`parseInput` 已将空输入当 0，高斯消元已覆盖奇异矩阵）：

在浏览器中逐一测试：

| 测试用例 | 增广矩阵 | 期望结果 |
|----------|----------|----------|
| 2阶标准 | [[1,1,3],[1,-1,1]] | x₁=2, x₂=1 |
| 3阶标准 | [[2,2,-1,5],[1,-1,2,3],[3,1,-1,2]] | 验证解正确 |
| 全零系数+非零常数 | [[0,0,1],[0,0,1]] | 无解 |
| 全零行 | [[1,1,1],[0,0,0]] | 有无穷多解 |
| 小数输入 | 0.5 1 0.5, 1 -1 1 | x₁=1, x₂=0 |
| 分数输入 | 1/2 1 1/2, 1 -1 1 | 同上 |
| 10阶 | 单位矩阵+[1,1,...] | x₁..x₁₀ = 1 |

- [ ] **Step 2: 提交**

```bash
git add index.html
git commit -m "chore: final validation"
```
