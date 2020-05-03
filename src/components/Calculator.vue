<template>
    <div class="calculator">
        <div class="result-screen">
            <span class="result-line expression">{{expression}}</span>
            <span class="result-line before-result" v-if="(!expressionTree.leftOperand && !expressionTree.operation)">enter expression</span>
            <span class="result-line result">{{result}}</span>
        </div>
        <div class="keyboard-container" v-on:click="onClick">
            <div>
                <button class="btn clear">C</button>
                <button class="btn erase">&#x2B05;</button>
                <button class="btn operation division" data-operation="÷">÷</button>
            </div>
            <div>
                <button class="btn number" data-num="1">1</button>
                <button class="btn number" data-num="2">2</button>
                <button class="btn number" data-num="3">3</button>
                <button class="btn operation multiply" data-operation="×">×</button>
            </div>
            <div>
                <button class="btn number" data-num="4">4</button>
                <button class="btn number" data-num="5">5</button>
                <button class="btn number" data-num="6">6</button>
                <button class="btn operation subtraction" data-operation="−">−</button>
            </div>
            <div>
                <button class="btn number" data-num="7">7</button>
                <button class="btn number" data-num="8">8</button>
                <button class="btn number" data-num="9">9</button>
                <button class="btn operation plus" data-operation="+">+</button>
            </div>
            <div>
                <button class="btn number" data-num="0">0</button>
                <button class="btn number" data-num="00">00</button>
                <button class="btn dot">.</button>
                <button class="btn operation equals" data-equals="equals">=</button>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'calculator',
        props: {},

        data() {
            return {
                userInput: {
                    type: '',
                    value: ''
                },

                expressionTree: {
                    leftOperand: '',
                    operation: '',
                    rightOperand: '',

                },

                result: '',
            }
        },

        computed: {
            expression() {
                return this.printSingleExpression(this.expressionTree);
            }
        },

        mounted() {
            window.addEventListener('keyup', this.onKeyUp)
        },

        destroyed() {
            window.removeEventListener('keyup', this.onKeyUp)
        },

        methods: {

            onClick(event) {

                let clickedEl = event.target;

                if (clickedEl.hasAttribute("data-num")) {
                    this.userInput.type = 'number';
                    this.userInput.value = clickedEl.getAttribute("data-num");

                } else if (clickedEl.hasAttribute("data-operation")) {
                    this.userInput.type = 'operation';
                    this.userInput.value = clickedEl.getAttribute("data-operation");

                } else if (clickedEl.classList.contains("dot")) {
                    this.userInput.type = 'dot';
                    this.userInput.value = '.';

                } else if (clickedEl.hasAttribute("data-equals")) {
                    this.userInput.type = 'equals';
                    this.userInput.value = '';

                } else if (clickedEl.classList.contains("erase")) {
                    this.userInput.type = 'erase';
                    this.userInput.value = '';

                } else if (clickedEl.classList.contains("clear")) {
                    this.userInput.type = 'clear';
                    this.userInput.value = '';

                }

                console.log(this.userInput.type);
                console.log(this.userInput.value);

                this.createExpressionTree();

            },

            onKeyUp(event) {

                let clickedElCode = event.key;

                if (clickedElCode >= 0 && clickedElCode <= 9) {
                    this.userInput.type = 'number';
                    this.userInput.value = clickedElCode;

                } else if (clickedElCode === '*' || clickedElCode === '/' || clickedElCode === '+' || clickedElCode === '-') {
                    this.userInput.type = 'operation';

                    // eslint-disable-next-line
                    // debugger;

                    switch(clickedElCode) {
                        case '*':
                            this.userInput.value = '×';
                            break;

                        case '/':
                            this.userInput.value = '÷';
                            break;

                        case '+':
                            this.userInput.value = '+';
                            break;
                        case '-':
                            this.userInput.value = '−';
                            break;
                    }

                } else if (clickedElCode === '.') {
                    this.userInput.type = 'dot';
                    this.userInput.value = '.';

                } else if (clickedElCode === '=' || clickedElCode === 'Enter') {
                    this.userInput.type = 'equals';
                    this.userInput.value = '=';

                } else if (clickedElCode === 'Backspace') {
                    this.userInput.type = 'erase';
                    this.userInput.value = '';

                } else if (clickedElCode === 'Delete') {
                    this.userInput.type = 'clear';
                    this.userInput.value = '';
                }

                console.log(clickedElCode);
                this.createExpressionTree();

            },

            printSingleExpression(expression) {
                let expressionString = '';

                if (typeof expression.leftOperand === 'object') {
                    expressionString += this.printSingleExpression(expression.leftOperand);
                } else {
                    expressionString += expression.leftOperand;
                }


                expressionString += expression.operation;

                if (typeof expression.rightOperand === 'object') {
                    expressionString += `(${this.printSingleExpression(expression.rightOperand)})`;
                } else {
                    expressionString += expression.rightOperand;
                }

                return expressionString;
            },

            createExpressionTree: function () {

                if (this.result && this.userInput.type === 'operation') {
                    this.expressionTree = {
                        leftOperand: this.result,
                        operation: '',
                        rightOperand: ''

                    };

                    this.result = '';

                } else if (this.result) {
                    this.clearAll()
                }

                switch (this.userInput.type) {
                    case 'number': {
                        if (this.expressionTree.operation.length === 0) {
                            this.expressionTree.leftOperand += this.userInput.value;
                        } else {
                            if (typeof this.expressionTree.rightOperand !== 'object') {
                                this.expressionTree.rightOperand += this.userInput.value;
                            } else {
                                this.expressionTree.rightOperand.rightOperand += this.userInput.value;
                            }
                        }

                        break;
                    }

                    case 'operation': {
                        const operation = this.userInput.value;

                        //!this.expressionTree.leftOperand - empty
                        if (!this.expressionTree.leftOperand && !this.expressionTree.rightOperand && (operation !== '+' && operation !== '−')) {
                            return;
                        }

                        if (this.expressionTree.rightOperand) {

                            if (this.expressionTree.rightOperand.operation && this.expressionTree.rightOperand.rightOperand.length === 0) {
                                return;
                            }

                            if (operation === '×' || operation === '÷') {

                                this.expressionTree = {
                                    leftOperand: this.expressionTree.leftOperand,
                                    operation: this.expressionTree.operation,
                                    rightOperand: {
                                        leftOperand: this.expressionTree.rightOperand,
                                        operation: operation,
                                        rightOperand: ''
                                    },

                                };

                            } else {
                                this.expressionTree = {
                                    leftOperand: {
                                        leftOperand: this.expressionTree.leftOperand,
                                        operation: this.expressionTree.operation,
                                        rightOperand: this.expressionTree.rightOperand,
                                    },
                                    operation: operation,
                                    rightOperand: ''

                                };
                            }

                        } else {
                            this.expressionTree.operation = operation;
                        }
                        break;
                    }

                    case 'dot': {
                        if (this.expressionTree.leftOperand.length >= 0 && this.expressionTree.rightOperand.length === 0) {
                            if (this.expressionTree.leftOperand.includes('.')) {
                                return;
                            }

                            this.expressionTree.leftOperand = this.expressionTree.leftOperand + '.';

                        } else if (this.expressionTree.rightOperand.length >= 0) {
                            if (this.expressionTree.rightOperand.includes('.')) {
                                return;
                            }

                            this.expressionTree.rightOperand = this.expressionTree.rightOperand + '.';
                        }

                        break;
                    }

                    case 'equals': {
                        this.result = this.performOperation(this.expressionTree.leftOperand, this.expressionTree.operation, this.expressionTree.rightOperand);
                        break;
                    }

                    case 'erase': {
                        this.erase(this.expressionTree);
                        break;
                    }

                    case 'clear': {
                        this.clearAll();
                        break;
                    }

                }
            },

            performOperation(leftOperand, operation, rightOperand) {
                if (typeof leftOperand === 'object') {
                    leftOperand = this.performOperation(leftOperand.leftOperand, leftOperand.operation, leftOperand.rightOperand);
                }

                if (typeof rightOperand === 'object') {
                    rightOperand = this.performOperation(rightOperand.leftOperand, rightOperand.operation, rightOperand.rightOperand);
                }

                leftOperand = leftOperand ? parseFloat(leftOperand) : 0;
                rightOperand = rightOperand ? parseFloat(rightOperand) : 0;

                switch (operation) {
                    case "+":
                        return Number((leftOperand + rightOperand).toFixed(10));

                    case "−":
                        return Number((leftOperand - rightOperand).toFixed(10));

                    case "×":
                        return Number((leftOperand * rightOperand).toFixed(10));

                    case "÷":
                        return Number((leftOperand / rightOperand).toFixed(10));
                }
            },

            clearAll() {
                this.expressionTree.leftOperand = this.expressionTree.rightOperand = this.expressionTree.operation = '';
                this.result = '';
            },

            erase(expression) {

                if (expression.rightOperand) {
                    if (typeof expression.rightOperand === 'object') {
                        this.erase(expression.rightOperand);

                    } else {
                        expression.rightOperand = expression.rightOperand.slice(0, -1);
                    }

                } else if (expression.operation) {
                    expression.operation = '';

                } else {
                    if (typeof expression.leftOperand === 'object') {
                        this.erase(expression.leftOperand);

                    } else {
                        expression.leftOperand = expression.leftOperand.slice(0, -1);
                    }
                }

                if (typeof expression.leftOperand === 'object' && !expression.operation) {
                    this.expressionTree = expression.leftOperand;
                }

                if (typeof expression.rightOperand === 'object' && !expression.rightOperand.operation) {
                    this.expressionTree = {
                        leftOperand: expression.leftOperand,
                        operation: expression.operation,
                        rightOperand: expression.rightOperand.leftOperand
                    }

                }
            }
        },
    }
</script>
<style scoped lang="less">

    .calculator {
        max-width: 100%;
        width: 330px;
        margin: 25px auto;
        background-color: #37404af7;
        padding: 15px;

        .btn {
            color: white;
            margin: 2px;
            padding: 15px;
            background-color: #37404a;
            font-weight: 900;
            width: 75px;
            height: 75px;
            font-size: 14px;
            transition: 0.2ms;

            &:hover {
                background-color: #1c6cbd5e;
            }
        }

        .result-screen {
            display: flex;
            align-items: flex-end;
            justify-content: center;
            flex-direction: column;
            height: 112px;
            padding: 5px;
            margin: 10px 10px 0;

            .result-line {
                text-align: right;
                display: block;

            }

            .expression {
                font-size: 27px;
                color: white;
            }

            .before-result {
                font-size: 27px;
                color: #65656591;
            }

            .result {
                font-size: 36px;
                color: #e42690;
            }
        }

        .keyboard-container {
            color: white;
            margin: -2px 0 8px;

            .clear {
                background-color: #e42690;
                width: 152px;
            }

            .equals {
                background-color: #1d6cbd;
            }
        }
    }

</style>
