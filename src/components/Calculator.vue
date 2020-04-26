<template>
    <div class="calculator">
        <div class="result-screen">
            <span class="result-line expression">{{expression}}</span>
            <span class="result-line result">{{result}}</span>
        </div>
        <div class="keyboard-container" v-on:click="identifyClickedKey($event)">
            <div>
                <button class="btn clear">C</button>
                <button class="btn erase">Erase</button>
                <button class="btn operation division" data-operation="division">/</button>
            </div>
            <div>
                <button class="btn number" data-num="1">1</button>
                <button class="btn number" data-num="2">2</button>
                <button class="btn number" data-num="3">3</button>
                <button class="btn operation multiply" data-operation="multiply">*</button>
            </div>
            <div>
                <button class="btn number" data-num="4">4</button>
                <button class="btn number" data-num="5">5</button>
                <button class="btn number" data-num="6">6</button>
                <button class="btn operation subtraction" data-operation="minus">-</button>
            </div>
            <div>
                <button class="btn number" data-num="7">7</button>
                <button class="btn number" data-num="8">8</button>
                <button class="btn number" data-num="9">9</button>
                <button class="btn operation plus" data-operation="plus">+</button>
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
                return this.expressionTree.leftOperand + this.expressionTree.operation + this.expressionTree.rightOperand;
            }
        },

        methods: {
            identifyClickedKey: function (event) {

                let clickedEl = event.target;

                if (this.result) {
                    this.clearAll();
                }


                if (clickedEl.hasAttribute("data-num")) {
                    this.expressionTree.operation.length === 0 ?
                        this.expressionTree.leftOperand += clickedEl.getAttribute("data-num")
                        : this.expressionTree.rightOperand += clickedEl.getAttribute("data-num");

                } else if (clickedEl.hasAttribute("data-operation")) {
                    this.expressionTree.operation = clickedEl.getAttribute("data-operation");

                } else if (clickedEl.classList.contains("dot")) {
                    if(this.expressionTree.leftOperand.length >= 0 && this.expressionTree.rightOperand.length === 0) {
                        this.expressionTree.leftOperand = this.expressionTree.leftOperand + '.';
                    } else if (this.expressionTree.rightOperand.length >= 0) {
                        this.expressionTree.rightOperand = this.expressionTree.rightOperand + '.';
                    }

                } else if (clickedEl.hasAttribute("data-equals")) {

                    this.performOperation(this.expressionTree.leftOperand, this.expressionTree.operation, this.expressionTree.rightOperand);
                } else if (clickedEl.classList.contains("erase")) {
                    this.erase();
                    return;
                } else if (clickedEl.classList.contains("clear")) {
                    this.clearAll();
                    return;
                }
            },

            performOperation(leftOperand, operation, rightOperand) {
                leftOperand = parseFloat(leftOperand);
                rightOperand = parseFloat(rightOperand);

                switch (operation) {
                    case "plus":
                        this.result = +(leftOperand + rightOperand).toFixed(10);
                        break;

                    case "minus":
                        this.result = +(leftOperand - rightOperand).toFixed(10);
                        break;

                    case "multiply":
                        this.result = +(leftOperand * rightOperand).toFixed(10);
                        break;

                    case "division":
                        this.result = +(leftOperand / rightOperand).toFixed(10);
                        break;
                }
            },

            clearAll() {
                this.expressionTree.leftOperand = this.expressionTree.rightOperand = this.expressionTree.operation = '';
                this.result = '';
            },

            erase() {

                if (this.expressionTree.rightOperand) {
                    this.expressionTree.rightOperand = this.expressionTree.rightOperand.slice(0, -1);

                } else if (this.expressionTree.operation) {
                    this.expressionTree.operation = '';

                } else {
                    this.expressionTree.leftOperand = this.expressionTree.leftOperand.slice(0, -1);
                }

                this.expression ? this.expression.slice(0, -1) : this.expression;
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
            background-color: white;
            height: 112px;
            padding: 5px;
            margin: 0 10px;
            color: #e42690;

            .result-line {
                text-align: right;
                display: block;

            }

            .expression {
                font-size: 27px;
                color: #3d464f;
            }

            .result {
                font-size: 36px;
            }
        }

        .keyboard-container {
            color: white;
            margin-top: -2px;

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
