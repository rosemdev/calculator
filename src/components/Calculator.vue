<template>
    <div class="calculator">
        <div class="result-screen">
            <span class="result-line expression">{{expression}}</span>
            <span class="result-line before-result" v-if="(!expressionTree.leftOperand && !expressionTree.operation)">enter expression</span>
            <span class="result-line result">{{result}}</span>
        </div>
        <div class="keyboard-container" ref="keyboard" v-on:click="onClick">
            <div v-bind:class="['additional-operations', {'extended': isExtended}]">
                <button type="button" class="btn operation" data-operation="power">x<sup class="sup">y</sup></button>
                <button type="button" class="btn operation" data-operation="squareRoot">√x</button>
                <button type="button" class="btn operation" data-operation="toThe2Power">x<sup class="sup">2</sup>
                </button>
                <button type="button" class="btn operation" data-operation="percent">%</button>
            </div>
            <div>
                <button type="button" class="btn clear">C</button>
                <button type="button" class="btn erase">⬅</button>
                <button type="button" class="btn operation" data-operation="division">÷</button>
            </div>
            <div>
                <button type="button" class="btn number" data-number="1">1</button>
                <button type="button" class="btn number" data-number="2">2</button>
                <button type="button" class="btn number" data-number="3">3</button>
                <button type="button" class="btn operation" data-operation="multiply">×</button>
            </div>
            <div>
                <button type="button" class="btn number" data-number="4">4</button>
                <button type="button" class="btn number" data-number="5">5</button>
                <button type="button" class="btn number" data-number="6">6</button>
                <button type="button" class="btn operation" data-operation="subtraction">−</button>
            </div>
            <div>
                <button type="button" class="btn number" data-number="7">7</button>
                <button type="button" class="btn number" data-number="8">8</button>
                <button type="button" class="btn number" data-number="9">9</button>
                <button type="button" class="btn operation" data-operation="plus">+</button>
            </div>
            <div>
                <button type="button" v-bind:class="[isExtended ? 'extended' : 'not-extended', 'btn']"
                        v-on:click="isExtended=!isExtended">
                </button>
                <button type="button" class="btn number" data-number="0">0</button>
                <button type="button" class="btn dot">.</button>
                <button type="button" class="btn equal">=</button>
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

                // keyboard: {
                //     number: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9],
                //     operation: ['+', '-', '/', '*'],
                //     oneOperandOperations: ['√', '%'],
                //     equal: ['=', 'Enter'],
                //     dot: '.',
                //     clear: 'Delete',
                //     erase: 'Backspace',
                //
                // },

                keyboard: {
                    number: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9],
                    equal: ['=', 'Enter'],
                    dot: '.',
                    clear: 'Delete',
                    erase: 'Backspace',
                    operation: {
                        subtraction: {
                            name: 'subtraction',
                            isTwoOperands: true,
                            isFirstPriority: false,
                            symbol: '−',
                            keyboardKey: '-'
                        },
                        plus: {
                            name: 'plus',
                            isTwoOperands: true,
                            isFirstPriority: false,
                            symbol: '+',
                            keyboardKey: '+'
                        },
                        multiply: {
                            name: 'multiply',
                            isTwoOperands: true,
                            isFirstPriority: true,
                            symbol: '×',
                            keyboardKey: '*'
                        },
                        division: {
                            name: 'division',
                            isTwoOperands: true,
                            isFirstPriority: true,
                            symbol: '÷',
                            keyboardKey: '/'
                        },
                        power: {
                            name: 'power',
                            isTwoOperands: true,
                            isFirstPriority: true,
                            symbol: '^',
                            keyboardKey: 'Shift ^'
                        },

                        toThe2Power: {
                            name: 'toThe2Power',
                            isTwoOperands: false,
                            isFirstPriority: true,
                            symbol: '^',
                            keyboardKey: 'Shift ^'
                        },
                        percent: {
                            name: 'percent',
                            isTwoOperands: true,
                            isFirstPriority: true,
                            symbol: '%',
                            keyboardKey: 'Shift %'
                        },
                        squareRoot: {
                            name: 'squareRoot',
                            isTwoOperands: false,
                            isFirstPriority: true,
                            symbol: '√',
                            keyboardKey: ''
                        },
                    },
                },

                result: '',
                isExtended: true
            }
        },

        computed: {
            expression() {
                return this.printSingleExpression(this.expressionTree);
            }
        },

        mounted() {
            // DOM not updated yet
            this.$nextTick(() => {
                // DOM updated
                this.$refs.keyboard.focus();
                window.addEventListener('keyup', this.onKeyUp)
            });
        },

        destroyed() {
            window.removeEventListener('keyup', this.onKeyUp)
        },

        methods: {
            isKeyNumber(key) {
                return this.keyboard.number.includes(Number(key));
            },

            isOperationKey(key) {

                let operations = this.keyboard.operation;

                for (let operation in operations) {
                    if (key === operations[operation].keyboardKey) {
                        return operation;
                    }
                }
            },

            isKeyDot(key) {
                return this.keyboard.dot === key;
            },

            isKeyErase(key) {
                return this.keyboard.erase === key;
            },

            isKeyClear(key) {
                return this.keyboard.clear === key;
            },

            isKeyEqual(key) {
                return this.keyboard.equal.includes(key);
            },

            isControlKey(key) {
                return this.isKeyNumber(key) || this.isOperationKey(key) || this.isKeyDot(key) ||
                    this.isKeyErase(key) || this.isKeyClear(key) || this.isKeyEqual(key);
            },

            onClick(event) {

                let clickedEl = event.target;
                let operationName = clickedEl.dataset.operation;

                for (let key in this.keyboard) {
                    if (clickedEl.classList.contains(key)) {
                        this.userInput.type = key;

                        if (this.userInput.type === 'operation') {
                            this.userInput.value = this.keyboard.operation[operationName];
                            break;

                        } else {
                            this.userInput.value = clickedEl.dataset[key];
                            break;
                        }


                    }
                }

                this.createExpressionTree();

            },

            onKeyUp(event) {
                let clickedElCode = event.key;
                //eslint-disable-next-line
                // debugger;

                // Treat Enter as a click only when a control button is in focus.
                // Because a browser triggers 2 events in such case - click and keyup.
                if (this.$refs.keyboard.contains(event.target) && clickedElCode === 'Enter') {
                    return;
                }


                if (clickedElCode !== 'Enter' && this.isControlKey(clickedElCode)) {
                    let focusedEl = document.activeElement;
                    focusedEl.blur();
                }

                if (this.isKeyNumber(clickedElCode)) {
                    this.userInput.type = 'number';
                    this.userInput.value = clickedElCode;

                } else if (this.isOperationKey(clickedElCode)) {

                    let operationName = this.isOperationKey(clickedElCode);
                    console.log(operationName);
                    this.userInput.type = 'operation';
                    this.userInput.value = this.keyboard.operation[operationName];


                } else if (this.isKeyDot(clickedElCode)) {
                    this.userInput.type = 'dot';
                    this.userInput.value = '.';

                } else if (this.isKeyEqual(clickedElCode)) {
                    this.userInput.type = 'equal';
                    this.userInput.value = '';


                } else if (this.isKeyErase(clickedElCode)) {
                    this.userInput.type = 'erase';
                    this.userInput.value = '';

                } else if (this.isKeyClear(clickedElCode)) {
                    this.userInput.type = 'clear';
                    this.userInput.value = '';

                } else {
                    this.userInput.type = 'not-calculator';
                    this.userInput.value = '';
                }

                if (this.userInput.type === 'not-calculator') {
                    return;
                }

                this.createExpressionTree();

            },

            printSingleExpression(expression) {
                let expressionString = '';

                if (typeof expression.leftOperand === 'object') {
                    expressionString += this.printSingleExpression(expression.leftOperand);

                } else {
                    expressionString += expression.leftOperand;
                }

                if (expression.operation.symbol) {
                    expressionString += expression.operation.symbol;
                }


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

                } else if (this.result && (this.userInput.type === 'number' || this.userInput.type === 'dot')) {
                    this.clearAll();
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
                        //this code lets the user to input only + - √ operations before numbers
                        if (!this.expressionTree.leftOperand && !this.expressionTree.rightOperand &&
                            (operation.name !== 'plus' &&
                                operation.name !== 'subtraction' &&
                                operation.name !== 'squareRoot')) {
                            return;
                        }


                        if (this.expressionTree.rightOperand) {

                            if (this.expressionTree.rightOperand.operation && this.expressionTree.rightOperand.rightOperand.length === 0) {
                                return;
                            }

                            if (operation.isFirstPriority) {

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

                        } else if (this.expressionTree.leftOperand && !operation.isTwoOperands) {
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

                    case 'equal': {
                        this.result = this.performOperation(this.expressionTree);
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

            performOperation(dataObject) {
                console.log(dataObject);

                let leftOperand = dataObject.leftOperand;
                let rightOperand = dataObject.rightOperand;
                const operationName = dataObject.operation.name;

                if (typeof leftOperand === 'object') {
                    leftOperand = this.performOperation(leftOperand);
                }

                if (typeof rightOperand === 'object') {
                    rightOperand = this.performOperation(rightOperand);
                }


                leftOperand = leftOperand ? parseFloat(leftOperand) : 0;
                rightOperand = rightOperand ? parseFloat(rightOperand) : 0;

                switch (operationName) {
                    case "plus":
                        return Number((leftOperand + rightOperand).toFixed(10));

                    case "subtraction":
                        return Number((leftOperand - rightOperand).toFixed(10));

                    case "multiply":
                        return Number((leftOperand * rightOperand).toFixed(10));

                    case "division":
                        return Number((leftOperand / rightOperand).toFixed(10));

                    case "squareRoot":
                        return Number((Math.sqrt(rightOperand)).toFixed(10));

                    case "power":
                        return Number((leftOperand ** rightOperand).toFixed(10));

                    case "toThe2Power":
                        return Number((leftOperand ** 2).toFixed(10));
                }
            },

            clearAll() {
                this.expressionTree.leftOperand = this.expressionTree.rightOperand = this.expressionTree.operation = '';
                this.result = '';
            },

            erase(expression) {

                if (this.result) {
                    this.clearAll();
                }

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
        width: 320px;
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
            overflow-wrap: anywhere;

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
            margin: 0 0 8px;

            .additional-operations {
                opacity: 0;
                height: 0;
                pointer-events: none;
                transition: height 0.3s, opacity 0.1ms;
            }

            .additional-operations.extended {
                opacity: 1;
                height: 54px;
                pointer-events: auto;

                .btn {
                    height: 50px;
                    background-color: #6969694f;

                    &:hover {
                        background-color: #1c6cbd5e;
                    }
                }

                .sup {
                    font-size: 9px;
                }

            }

            .clear {
                background-color: #e42690;
                width: 152px;
            }

            .equal {
                background-color: #1d6cbd;
            }

            .btn.extended {
                &:after {
                    content: '⬋';
                }
            }

            .btn.not-extended {
                &:after {
                    content: '⬈';
                }
            }

        }
    }

</style>
