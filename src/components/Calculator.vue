<template>
    <div class="calculator">
        <div class="result-screen">
            <span class="result-line expression">2 + 2</span>
            <span class="result-line result">4</span>
        </div>
        <div class="keyboard-container">
            <div>
                <button class="btn clear">C</button>
                <button class="btn erase">Erase</button>
                <button class="btn operation division">/</button>
            </div>
            <div>
                <button class="btn number" data-num="1">1</button>
                <button class="btn number" data-num="2">2</button>
                <button class="btn number" data-num="3">3</button>
                <button class="btn operation multiply">*</button>
            </div>
            <div>
                <button class="btn number" data-num="4">4</button>
                <button class="btn number" data-num="5">5</button>
                <button class="btn number" data-num="6">6</button>
                <button class="btn operation subtraction">-</button>
            </div>
            <div>
                <button class="btn number" data-num="7">7</button>
                <button class="btn number" data-num="8">8</button>
                <button class="btn number" data-num="9">9</button>
                <button class="btn operation plus">+</button>
            </div>
            <div>
                <button class="btn number" data-num="0">0</button>
                <button class="btn number" data-num="0">00</button>
                <button class="btn dot">.</button>
                <button class="btn operation equals">=</button>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'calculator',
        props: {},

        data() {
            return {}
        },

        methods: {},
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
