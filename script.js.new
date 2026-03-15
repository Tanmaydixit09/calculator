class AdvancedCalculator {
    constructor() {
        this.input = document.getElementById('inputBox');
        this.history = document.getElementById('history') || null;
        this.currentInput = '';
        this.memory = 0;
        this.historyList = [];
        this.init();
    }

    init() {
        document.querySelectorAll('button').forEach(btn => {
            btn.addEventListener('click', (e) => this.handleButton(e.target.innerHTML));
        });
        document.addEventListener('keydown', (e) => this.handleKeyboard(e));
    }

    handleButton(value) {
        try {
            if (value === '=') {
                this.calculate();
            } else if (value === 'AC') {
                this.clear();
            } else if (value === 'DEL' || value === 'CE') {
                this.delete();
            } else if (['MC', 'MR', 'M+', 'M-'].includes(value)) {
                this.handleMemory(value);
            } else if (['sin', 'cos', 'tan', 'log', 'sqrt'].includes(value)) {
                this.handleUnary(value);
            } else if (value === 'π') {
                this.append('Math.PI');
            } else if (['(', ')'].includes(value)) {
                this.append(value);
            } else {
                this.append(value);
            }
            this.updateDisplay();
        } catch (error) {
            this.input.value = 'Error';
        }
    }

    handleKeyboard(e) {
        const key = e.key;
        if (/[0-9.]/.test(key) || ['+', '-', '*', '/', '(', ')', '='].includes(key)) {
            this.append(key === '*' ? '*' : key === '/' ? '/' : key);
        } else if (key === 'Enter') {
            this.calculate();
        } else if (key === 'Escape') {
            this.clear();
        } else if (key === 'Backspace') {
            this.delete();
        }
        this.updateDisplay();
    }

    append(value) {
        this.currentInput += value;
    }

    delete() {
        this.currentInput = this.currentInput.slice(0, -1);
    }

    clear() {
        this.currentInput = '';
        this.input.value = '0';
    }

    calculate() {
        try {
            const result = this.evaluate(this.currentInput);
            this.historyList.push(`${this.currentInput} = ${result}`);
            if (this.history) {
                this.history.innerHTML = this.historyList.slice(-5).map(h => `<div>${h}</div>`).join('');
            }
            this.currentInput = result.toString();
            this.input.value = result;
        } catch (e) {
            this.input.value = 'Error';
            this.currentInput = '';
        }
    }

    handleMemory(op) {
        switch (op) {
            case 'MC': this.memory = 0; break;
            case 'MR': this.append(this.memory.toString()); break;
            case 'M+': this.memory += parseFloat(this.currentInput || 0); break;
            case 'M-': this.memory -= parseFloat(this.currentInput || 0); break;
        }
    }

    handleUnary(fn) {
        try {
            const num = parseFloat(this.currentInput || 0);
            let result;
            switch (fn) {
                case 'sqrt': result = Math.sqrt(num); break;
                case 'sin': result = Math.sin(num * Math.PI / 180); break; // degrees
                case 'cos': result = Math.cos(num * Math.PI / 180); break;
                case 'tan': result = Math.tan(num * Math.PI / 180); break;
                case 'log': result = Math.log10(num); break;
            }
            this.currentInput = result.toString();
            this.updateDisplay();
        } catch (e) {
            this.input.value = 'Error';
        }
    }

    updateDisplay() {
        this.input.value = this.currentInput || '0';
    }

    // Shunting-yard algorithm for expression evaluation
    evaluate(expr) {
        // Replace π
        expr = expr.replace(/π/g, 'Math.PI');
        // Tokenize
        const tokens = this.tokenize(expr);
        const output = [];
        const operators = [];

        for (let token of tokens) {
            if (this.isNumber(token)) {
                output.push(token);
            } else if (this.isOperator(token)) {
                while (this.precedence(operators[operators.length - 1] || '') >= this.precedence(token)) {
                    output.push(operators.pop());
                }
                operators.push(token);
            } else if (token === '(') {
                operators.push(token);
            } else if (token === ')') {
                while (operators[operators.length - 1] !== '(') {
                    output.push(operators.pop());
                }
                operators.pop();
            }
        }

        while (operators.length) {
            output.push(operators.pop());
        }

        return this.evaluateRPN(output);
    }

    tokenize(expr) {
        return expr.match(/(\d+\.?\d*|\.\d+|[+\-*/()π])/g) || [];
    }

    isNumber(token) {
        return !isNaN(token) || token === 'Math.PI';
    }

    isOperator(token) {
        return ['+', '-', '*', '/'].includes(token);
    }

    precedence(op) {
        if (op === '+' || op === '-') return 1;
        if (op === '*' || op === '/') return 2;
        return 0;
    }

    evaluateRPN(tokens) {
        const stack = [];
        for (let token of tokens) {
            if (this.isNumber(token)) {
                stack.push(parseFloat(token === 'Math.PI' ? Math.PI : token));
            } else {
                const b = stack.pop();
                const a = stack.pop();
                switch (token) {
                    case '+': stack.push(a + b); break;
                    case '-': stack.push(a - b); break;
                    case '*': stack.push(a * b); break;
                    case '/': stack.push(a / b); break;
                }
            }
        }
        return stack[0] || 0;
    }
}

// Initialize
const calc = new AdvancedCalculator();
