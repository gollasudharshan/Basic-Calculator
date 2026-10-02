<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>My Calculator</title>

    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            font-family: Arial, sans-serif;

            background: linear-gradient(135deg, #667eea, #764ba2);
        }

        .calculator {
            width: 350px;
            padding: 20px;

            background-color: #1e1e1e;
            border-radius: 20px;

            box-shadow: 0 15px 35px rgba(0, 0, 0, 0.3);
        }

        .display {
            width: 100%;
            height: 80px;

            margin-bottom: 20px;
            padding: 15px;

            border: none;
            border-radius: 10px;

            background-color: #2d2d2d;
            color: white;

            font-size: 32px;
            text-align: right;

            outline: none;
        }

        .buttons {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 10px;
        }

        button {
            height: 65px;

            border: none;
            border-radius: 12px;

            background-color: #3a3a3a;
            color: white;

            font-size: 22px;

            cursor: pointer;

            transition: 0.2s;
        }

        button:hover {
            transform: scale(1.04);
            background-color: #4a4a4a;
        }

        button:active {
            transform: scale(0.95);
        }

        .operator {
            background-color: #ff9500;
        }

        .operator:hover {
            background-color: #e68600;
        }

        .equals {
            background-color: #28a745;
        }

        .equals:hover {
            background-color: #218838;
        }

        .clear {
            background-color: #dc3545;
        }

        .clear:hover {
            background-color: #c82333;
        }

        .special {
            background-color: #555;
        }

        /* Scientific buttons */
        .scientific {
            background-color: #555;
        }

        /* Mobile */
        @media (max-width: 400px) {

            .calculator {
                width: 95%;
            }

            button {
                height: 60px;
            }

            .display {
                font-size: 28px;
            }
        }
    </style>
</head>

<body>

    <div class="calculator">

        <!-- Display -->
        <input
            type="text"
            id="display"
            class="display"
            value="0"
            readonly
        >

        <!-- Buttons -->
        <div class="buttons">

            <!-- Row 1 -->
            <button class="clear" onclick="clearDisplay()">C</button>
            <button class="special" onclick="deleteLast()">⌫</button>
            <button class="special" onclick="percentage()">%</button>
            <button class="operator" onclick="chooseOperator('/')">÷</button>

            <!-- Row 2 -->
            <button onclick="appendNumber('7')">7</button>
            <button onclick="appendNumber('8')">8</button>
            <button onclick="appendNumber('9')">9</button>
            <button class="operator" onclick="chooseOperator('*')">×</button>

            <!-- Row 3 -->
            <button onclick="appendNumber('4')">4</button>
            <button onclick="appendNumber('5')">5</button>
            <button onclick="appendNumber('6')">6</button>
            <button class="operator" onclick="chooseOperator('-')">−</button>

            <!-- Row 4 -->
            <button onclick="appendNumber('1')">1</button>
            <button onclick="appendNumber('2')">2</button>
            <button onclick="appendNumber('3')">3</button>
            <button class="operator" onclick="chooseOperator('+')">+</button>

            <!-- Row 5 -->
            <button onclick="toggleSign()">+/-</button>
            <button onclick="appendNumber('0')">0</button>
            <button onclick="appendDecimal()">.</button>
            <button class="equals" onclick="calculate()">=</button>

        </div>

    </div>


    <script>

        // Get display
        const display = document.getElementById("display");

        // Calculator variables
        let firstNumber = null;
        let currentOperator = null;
        let waitingForSecondNumber = false;


        // Add number
        function appendNumber(number) {

            if (display.value === "Error") {
                clearDisplay();
            }

            if (waitingForSecondNumber) {

                display.value = number;

                waitingForSecondNumber = false;

            } else if (display.value === "0") {

                display.value = number;

            } else {

                display.value += number;
            }
        }


        // Decimal
        function appendDecimal() {

            if (display.value === "Error") {
                clearDisplay();
            }

            if (waitingForSecondNumber) {

                display.value = "0.";

                waitingForSecondNumber = false;

                return;
            }

            if (!display.value.includes(".")) {

                display.value += ".";
            }
        }


        // Choose operator
        function chooseOperator(operator) {

            const inputValue = parseFloat(display.value);

            if (isNaN(inputValue)) {
                return;
            }

            if (
                currentOperator !== null &&
                !waitingForSecondNumber
            ) {
                calculate();
            }

            firstNumber = parseFloat(display.value);

            currentOperator = operator;

            waitingForSecondNumber = true;
        }


        // Calculate
        function calculate() {

            if (
                currentOperator === null ||
                waitingForSecondNumber
            ) {
                return;
            }

            const secondNumber = parseFloat(display.value);

            let result;


            switch (currentOperator) {

                case "+":

                    result = firstNumber + secondNumber;

                    break;


                case "-":

                    result = firstNumber - secondNumber;

                    break;


                case "*":

                    result = firstNumber * secondNumber;

                    break;


                case "/":

                    if (secondNumber === 0) {

                        display.value = "Error";

                        firstNumber = null;

                        currentOperator = null;

                        waitingForSecondNumber = false;

                        return;
                    }

                    result = firstNumber / secondNumber;

                    break;
            }


            // Avoid long decimal values
            result = Number(result.toFixed(10));

            display.value = result;

            firstNumber = result;

            currentOperator = null;

            waitingForSecondNumber = true;
        }


        // Clear
        function clearDisplay() {

            display.value = "0";

            firstNumber = null;

            currentOperator = null;

            waitingForSecondNumber = false;
        }


        // Delete last character
        function deleteLast() {

            if (
                display.value === "Error" ||
                waitingForSecondNumber
            ) {
                clearDisplay();

                return;
            }

            if (display.value.length === 1) {

                display.value = "0";

            } else {

                display.value =
                    display.value.slice(0, -1);
            }
        }


        // Percentage
        function percentage() {

            if (display.value === "Error") {

                clearDisplay();
            }

            const number =
                parseFloat(display.value);

            if (isNaN(number)) {
                return;
            }

            display.value = number / 100;
        }


        // Positive / Negative
        function toggleSign() {

            if (display.value === "Error") {

                clearDisplay();
            }

            const number =
                parseFloat(display.value);

            if (
                isNaN(number) ||
                number === 0
            ) {
                return;
            }

            display.value = number * -1;
        }


        // Keyboard support
        document.addEventListener(
            "keydown",
            function(event) {

                const key = event.key;


                // Numbers
                if (
                    key >= "0" &&
                    key <= "9"
                ) {

                    appendNumber(key);

                    return;
                }


                // Decimal
                if (key === ".") {

                    appendDecimal();

                    return;
                }


                // Operators
                if (
                    key === "+" ||
                    key === "-" ||
                    key === "*" ||
                    key === "/"
                ) {

                    chooseOperator(key);

                    return;
                }


                // Enter
                if (
                    key === "Enter" ||
                    key === "="
                ) {

                    event.preventDefault();

                    calculate();

                    return;
                }


                // Backspace
                if (key === "Backspace") {

                    deleteLast();

                    return;
                }


                // Escape / C
                if (
                    key === "Escape" ||
                    key.toLowerCase() === "c"
                ) {

                    clearDisplay();

                    return;
                }


                // Percentage
                if (key === "%") {

                    percentage();
                }

            }
        );

    </script>

</body>

</html>


