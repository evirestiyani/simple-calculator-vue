<template>
    <div :class="['kalkulator', currentTheme]">
        <!-- Theme Toggle - Icon Only -->
        <div class="theme-toggle">
            <button @click="toggleTheme" class="toggle-btn">
                <span class="toggle-icon">{{ currentTheme === 'dark' ? '☀️' : '🌙' }}</span>
            </button>
        </div>

        <div class="header">
            <h2>Calculator</h2>
        </div>
        
        <!-- Display Screen -->
        <div class="display">
            <div class="expression">{{ expression || '0' }}</div>
            <div class="input-display">{{ inputDisplay || '0' }}</div>
            <div class="result-display">{{ result }}</div>
        </div>

        <!-- Button Grid -->
        <div class="button-grid">
            <!-- Row 1: Clear, Delete, Operations -->
            <button @click="clear" class="btn btn-function btn-clear">
                <span>AC</span>
            </button>
            <button @click="deleteLast" class="btn btn-function">
                <span>⌫</span>
            </button>
            <button @click="addOperator('%')" class="btn btn-function">
                <span>%</span>
            </button>
            <button @click="addOperator('/')" class="btn btn-operator">
                <span>÷</span>
            </button>

            <!-- Row 2: Numbers 7,8,9 and multiply -->
            <button @click="addNumber('7')" class="btn btn-number">
                <span>7</span>
            </button>
            <button @click="addNumber('8')" class="btn btn-number">
                <span>8</span>
            </button>
            <button @click="addNumber('9')" class="btn btn-number">
                <span>9</span>
            </button>
            <button @click="addOperator('*')" class="btn btn-operator">
                <span>×</span>
            </button>

            <!-- Row 3: Numbers 4,5,6 and subtract -->
            <button @click="addNumber('4')" class="btn btn-number">
                <span>4</span>
            </button>
            <button @click="addNumber('5')" class="btn btn-number">
                <span>5</span>
            </button>
            <button @click="addNumber('6')" class="btn btn-number">
                <span>6</span>
            </button>
            <button @click="addOperator('-')" class="btn btn-operator">
                <span>−</span>
            </button>

            <!-- Row 4: Numbers 1,2,3 and add -->
            <button @click="addNumber('1')" class="btn btn-number">
                <span>1</span>
            </button>
            <button @click="addNumber('2')" class="btn btn-number">
                <span>2</span>
            </button>
            <button @click="addNumber('3')" class="btn btn-number">
                <span>3</span>
            </button>
            <button @click="addOperator('+')" class="btn btn-operator">
                <span>+</span>
            </button>

            <!-- Row 5: 0, decimal, equals -->
            <button @click="addNumber('0')" class="btn btn-number btn-zero">
                <span>0</span>
            </button>
            <button @click="addDecimal" class="btn btn-number">
                <span>.</span>
            </button>
            <button @click="calculate" class="btn btn-equals">
                <span>=</span>
            </button>
        </div>
    </div>
</template>

<script>
export default {
  data() {
    return {
      inputDisplay: '',
      result: '',
      expression: '',
      operator: '',
      operand1: '',
      waitingForOperand: false,
      currentTheme: 'dark'
    }
  },
  methods: {
    toggleTheme() {
      this.currentTheme = this.currentTheme === 'dark' ? 'premium' : 'dark'
    },
    
    addNumber(num) {
      if (this.waitingForOperand) {
        this.inputDisplay = num
        this.waitingForOperand = false
      } else {
        this.inputDisplay = this.inputDisplay === '0' ? num : this.inputDisplay + num
      }
      this.updateExpression()
    },
    
    addDecimal() {
      if (this.waitingForOperand) {
        this.inputDisplay = '0.'
        this.waitingForOperand = false
      } else if (this.inputDisplay.indexOf('.') === -1) {
        this.inputDisplay += '.'
      }
      this.updateExpression()
    },
    
    addOperator(nextOperator) {
      const inputValue = parseFloat(this.inputDisplay)
      
      if (this.operand1 === '') {
        this.operand1 = inputValue
      } else if (this.operator && !this.waitingForOperand) {
        const newValue = this.performCalculation()
        this.inputDisplay = String(newValue)
        this.operand1 = newValue
      }
      
      this.waitingForOperand = true
      this.operator = nextOperator
      this.updateExpression()
    },
    
    calculate() {
      const inputValue = parseFloat(this.inputDisplay)
      
      if (this.operand1 !== '' && this.operator && !this.waitingForOperand) {
        const newValue = this.performCalculation()
        this.inputDisplay = String(newValue)
        this.result = `= ${this.formatNumber(newValue)}`
        this.expression = `${this.operand1} ${this.getOperatorSymbol(this.operator)} ${inputValue} =`
        this.operand1 = newValue
        this.waitingForOperand = true
        this.operator = ''
      }
    },
    
    performCalculation() {
      const prev = parseFloat(this.operand1)
      const current = parseFloat(this.inputDisplay)
      
      switch (this.operator) {
        case '+':
          return prev + current
        case '-':
          return prev - current
        case '*':
          return prev * current
        case '/':
          return current !== 0 ? prev / current : 0
        case '%':
          return prev % current
        default:
          return current
      }
    },
    
    clear() {
      this.inputDisplay = '0'
      this.result = ''
      this.expression = ''
      this.operator = ''
      this.operand1 = ''
      this.waitingForOperand = false
    },
    
    deleteLast() {
      if (this.inputDisplay.length > 1) {
        this.inputDisplay = this.inputDisplay.slice(0, -1)
      } else {
        this.inputDisplay = '0'
      }
      this.updateExpression()
    },
    
    updateExpression() {
      if (this.operand1 && this.operator) {
        this.expression = `${this.operand1} ${this.getOperatorSymbol(this.operator)}`
      } else {
        this.expression = ''
      }
    },
    
    getOperatorSymbol(op) {
      const symbols = {
        '+': '+',
        '-': '−',
        '*': '×',
        '/': '÷',
        '%': '%'
      }
      return symbols[op] || op
    },
    
    formatNumber(num) {
      if (num % 1 === 0) {
        return num.toString()
      }
      return parseFloat(num.toFixed(8)).toString()
    }
  }
}
</script>

<style scoped>
* {
  box-sizing: border-box;
}

.app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, #0f0f23, #1a1a2e, #16213e);
  font-family: 'SF Pro Display', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  padding: 20px;
  position: relative;
}

.app::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: radial-gradient(circle at 20% 50%, rgba(120, 119, 198, 0.1) 0%, transparent 50%),
              radial-gradient(circle at 80% 20%, rgba(255, 119, 198, 0.1) 0%, transparent 50%);
  pointer-events: none;
}

/* Theme Toggle - Positioned at top right */
.theme-toggle {
  position: absolute;
  top: 20px;
  right: 20px;
  z-index: 10;
}

.toggle-btn {
  width: 50px;
  height: 50px;
  border: none;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.1);
  cursor: pointer;
  backdrop-filter: blur(20px);
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid rgba(255, 255, 255, 0.1);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.2);
}

.toggle-btn:hover {
  background: rgba(255, 255, 255, 0.2);
  transform: translateY(-3px) scale(1.05);
  box-shadow: 0 15px 35px rgba(0, 0, 0, 0.3);
}

.toggle-btn:active {
  transform: translateY(-1px) scale(0.98);
}

.toggle-icon {
  font-size: 22px;
  transition: transform 0.3s ease;
}

.toggle-btn:hover .toggle-icon {
  transform: rotate(15deg);
}

/* CALCULATOR BASE */
.kalkulator {
  width: 340px;
  padding: 30px;
  border-radius: 30px;
  backdrop-filter: blur(30px);
  border: 1px solid rgba(255, 255, 255, 0.1);
  position: relative;
  overflow: hidden;
}

.kalkulator::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(135deg, rgba(255, 255, 255, 0.1) 0%, rgba(255, 255, 255, 0.05) 100%);
  pointer-events: none;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 25px;
}

.header h2 {
  margin: 0;
  font-weight: 300;
  font-size: 26px;
  letter-spacing: 1px;
}

.indicator {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0%, 100% { opacity: 0.6; }
  50% { opacity: 1; }
}

/* DISPLAY */
.display {
  border-radius: 20px;
  padding: 25px;
  margin-bottom: 25px;
  backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 255, 255, 0.1);
  position: relative;
}

.expression {
  font-size: 14px;
  text-align: right;
  min-height: 20px;
  opacity: 0.7;
  margin-bottom: 5px;
}

.input-display {
  font-size: 36px;
  text-align: right;
  min-height: 45px;
  font-weight: 300;
  margin: 10px 0;
}

.result-display {
  font-size: 16px;
  text-align: right;
  min-height: 20px;
  margin-top: 10px;
  opacity: 0.8;
}

/* BUTTON GRID */
.button-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: repeat(5, 1fr);
  gap: 15px;
  height: 380px;
}

.btn {
  border: none;
  border-radius: 18px;
  font-size: 22px;
  font-weight: 400;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  user-select: none;
  position: relative;
  overflow: hidden;
  backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.btn span {
  position: relative;
  z-index: 2;
}

.btn::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(135deg, rgba(255, 255, 255, 0.1) 0%, rgba(255, 255, 255, 0.05) 100%);
  opacity: 0;
  transition: opacity 0.3s ease;
}

.btn:hover::before {
  opacity: 1;
}

.btn:active {
  transform: scale(0.95);
}

.btn-zero {
  grid-column: span 2;
}

/* DARK THEME */
.kalkulator.dark {
  background: linear-gradient(135deg, 
    rgba(26, 26, 26, 0.9) 0%, 
    rgba(45, 45, 45, 0.9) 100%);
  box-shadow: 
    0 30px 60px rgba(0, 0, 0, 0.5),
    inset 0 1px 0 rgba(255, 255, 255, 0.1);
}

.dark .toggle-btn {
  background: rgba(255, 255, 255, 0.1);
}

.dark .toggle-btn:hover {
  background: rgba(255, 255, 255, 0.2);
}

.dark .header h2 {
  color: #ffffff;
}


.dark .display {
  background: linear-gradient(135deg, 
    rgba(15, 15, 15, 0.9) 0%, 
    rgba(31, 31, 31, 0.9) 100%);
  box-shadow: inset 0 2px 10px rgba(0, 0, 0, 0.5);
}

.dark .expression {
  color: #888;
}

.dark .input-display {
  color: #ffffff;
}

.dark .result-display {
  color: #64ffda;
}

.dark .btn-number {
  background: linear-gradient(135deg, 
    rgba(58, 58, 58, 0.8) 0%, 
    rgba(42, 42, 42, 0.8) 100%);
  color: #ffffff;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.3);
}

.dark .btn-number:hover {
  background: linear-gradient(135deg, 
    rgba(74, 74, 74, 0.9) 0%, 
    rgba(58, 58, 58, 0.9) 100%);
  transform: translateY(-2px);
  box-shadow: 0 12px 25px rgba(0, 0, 0, 0.4);
}

.dark .btn-operator {
  background: linear-gradient(135deg, #ff6b6b, #ee5a24);
  color: white;
  box-shadow: 0 8px 20px rgba(255, 107, 107, 0.3);
}

.dark .btn-operator:hover {
  background: linear-gradient(135deg, #ff5252, #d32f2f);
  transform: translateY(-2px);
  box-shadow: 0 12px 25px rgba(255, 82, 82, 0.4);
}

.dark .btn-function {
  background: linear-gradient(135deg, 
    rgba(102, 102, 102, 0.8) 0%, 
    rgba(85, 85, 85, 0.8) 100%);
  color: white;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.3);
}

.dark .btn-function:hover {
  background: linear-gradient(135deg, 
    rgba(119, 119, 119, 0.9) 0%, 
    rgba(102, 102, 102, 0.9) 100%);
  transform: translateY(-2px);
}

.dark .btn-clear {
  background: linear-gradient(135deg, #ff4757, #c44569);
}

.dark .btn-equals {
  background: linear-gradient(135deg, #4caf50, #388e3c);
  color: white;
  box-shadow: 0 8px 20px rgba(76, 175, 80, 0.3);
}

.dark .btn-equals:hover {
  background: linear-gradient(135deg, #66bb6a, #4caf50);
  transform: translateY(-2px);
  box-shadow: 0 12px 25px rgba(102, 187, 106, 0.4);
}

/* PREMIUM THEME (Light Mode) */
.kalkulator.premium {
  background: linear-gradient(135deg, 
    rgba(255, 255, 255, 0.9) 0%, 
    rgba(248, 248, 248, 0.9) 100%);
  box-shadow: 
    0 30px 60px rgba(0, 0, 0, 0.1),
    inset 0 1px 0 rgba(218, 165, 32, 0.2);
}

.premium .toggle-btn {
  background: rgba(0, 0, 0, 0.1);
}

.premium .toggle-btn:hover {
  background: rgba(0, 0, 0, 0.15);
}

.premium .header h2 {
  color: #333;
}

.premium .indicator {
  background: #ffd700;
}

.premium .display {
  background: linear-gradient(135deg, 
    rgba(255, 255, 255, 0.9) 0%, 
    rgba(248, 248, 248, 0.9) 100%);
  box-shadow: inset 0 2px 10px rgba(0, 0, 0, 0.05);
  border: 1px solid rgba(0, 0, 0, 0.1);
}

.premium .expression {
  color: rgba(0, 0, 0, 0.6);
}

.premium .input-display {
  color: #333;
}

.premium .result-display {
  color: #f39c12;
}

.premium .btn-number {
  background: linear-gradient(135deg, #ffffff 0%, #f8f9fa 100%);
  color: #333;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  border: 1px solid rgba(0, 0, 0, 0.1);
}

.premium .btn-number:hover {
  background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
  transform: translateY(-2px);
  box-shadow: 0 8px 18px rgba(0, 0, 0, 0.15);
}

.premium .btn-operator {
  background: linear-gradient(135deg, #3498db, #2980b9);
  color: white;
  box-shadow: 0 4px 12px rgba(52, 152, 219, 0.3);
}

.premium .btn-operator:hover {
  background: linear-gradient(135deg, #5dade2, #3498db);
  transform: translateY(-2px);
  box-shadow: 0 8px 18px rgba(52, 152, 219, 0.4);
}

.premium .btn-function {
  background: linear-gradient(135deg, #95a5a6, #7f8c8d);
  color: white;
  box-shadow: 0 4px 12px rgba(149, 165, 166, 0.3);
}

.premium .btn-function:hover {
  background: linear-gradient(135deg, #bdc3c7, #95a5a6);
  transform: translateY(-2px);
}

.premium .btn-clear {
  background: linear-gradient(135deg, #e74c3c, #c0392b);
  color: white;
}

.premium .btn-equals {
  background: linear-gradient(135deg, #27ae60, #229954);
  color: white;
  box-shadow: 0 4px 12px rgba(39, 174, 96, 0.3);
}

.premium .btn-equals:hover {
  background: linear-gradient(135deg, #58d68d, #27ae60);
  transform: translateY(-2px);
  box-shadow: 0 8px 20px rgba(39, 174, 96, 0.4);
}

/* RESPONSIVE */
@media (max-width: 480px) {
  .theme-toggle {
    top: 15px;
    right: 15px;
  }
  
  .toggle-btn {
    width: 45px;
    height: 45px;
  }
  
  .toggle-icon {
    font-size: 20px;
  }

  .kalkulator {
    width: 95%;
    max-width: 320px;
    padding: 20px;
  }

  .button-grid {
    gap: 10px;
    height: 350px;
  }

  .btn {
    font-size: 20px;
    border-radius: 15px;
  }

  .display {
    padding: 20px;
  }

  .input-display {
    font-size: 30px !important;
  }

  .header h2 {
    font-size: 22px;
  }
}

@media (max-width: 360px) {
  .toggle-btn {
    width: 40px;
    height: 40px;
  }
  
  .toggle-icon {
    font-size: 18px;
  }

  .kalkulator {
    padding: 15px;
  }

  .button-grid {
    gap: 8px;
    height: 320px;
  }

  .btn {
    font-size: 18px;
  }

  .input-display {
    font-size: 26px !important;
  }
}
</style>