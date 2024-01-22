<template>
  <div class="calculator">
    <CalculatorDisplay :input="formula" :result="screenValue" />
    <div class="grid">
      <div class="buttons">
        <CalculatorButton v-for="button in ['7', '8', '9', '4', '5', '6', '1', '2', '3', '0', '.', 'del']" :key="button"
          :label="button" :class="{ 'number-button': true, 'button-item': true }" @button-click="addDigit" />
      </div>
      <div class="buttons grid-2-columns">
        <CalculatorButton v-for="button in ['%', 'AC', '-', '+', '=', 'x']" :key="button" :label="button"
          :class="{ 'equals-button': (button === '=' || button == 'AC'), 'button-item': true }"
          @button-click="addExpression" />
      </div>
    </div>
  </div>
</template>
  
<script>
import CalculatorButton from './Button.vue';
import CalculatorDisplay from './Display.vue';

function getInitialValues() {
  return {
    formula: '0',
    screenValue: '0',
    numericValues: [0, 0],
    expression: [null, null],
    currentValue: 0,
    resetScreenValue: false
  };
}

export default {
  name: 'AppCalculator',
  components: {
    CalculatorButton,
    CalculatorDisplay,
  },
  data() {
    return getInitialValues();
  },
  methods: {
    addDigit(value) {
      // Check if the screen needs to be reset
      if ((this.resetScreenValue && this.formula.includes('=') && this.expression[1] === '=') || this.expression[1] === '=') {
        this.clearValues();
      }

      // Handling for the decimal point
      if (value === '.' && this.screenValue.includes('.')) {
        return;
      }

      // Handling for the 'del' button
      if (value === 'del') {

        // do not delete if it is the result of the calculation
        if (this.expression[1] === '=') {
          return;
        }
        this.handleDelete();

      } else {
        // Handling for other digit inputs
        this.handleDigitInput(value);
      }

      // Set the flag to indicate that screen value reset is not required
      this.resetScreenValue = false;
    },

    handleDelete() {
      // Extract the last input from the formula
      const lastInput = this.formula.slice(-this.screenValue.length);

      // Check if the last input matches the current screen value
      if (lastInput === this.screenValue) {
        // Update screen and formula values after deletion
        this.screenValue = this.screenValue.length === 1 ? '0' : this.screenValue.slice(0, -1);
        this.formula = this.formula.length === 1 ? '0' : this.formula.slice(0, -1);
      }
    },

    handleDigitInput(value) {
      // Resetting screen value if needed
      this.screenValue = this.resetScreenValue ? '' : this.screenValue;

      // Remove leading zeros
      this.screenValue = this.screenValue === '0' ? '' : this.screenValue;
      this.formula = this.formula === '0' ? '' : this.formula;

      // Update screen and formula values with the new digit
      this.screenValue += value;
      this.formula += value;

      // Update the numeric values array
      this.numericValues[this.currentValue] = parseFloat(this.screenValue);
    },
    addExpression(value) {

      // If you have already pressed any operation
      if (this.formula.slice(-1) === value) {
        return;
      }

      // Update the previous operation with the value of the current operation
      this.expression[0] = this.expression[1];

      // Set the current operation to the new value
      this.expression[1] = value;

      // Switch statement to handle different button inputs based on the provided 'value'.
      switch (value) {
        // Case 'AC': Clear all values and reset the calculator.
        case 'AC':
          this.clearValues();
          return;

        // Case '=': Evaluate the expression and update the calculator state.
        case '=':
          this.evaluateExpression();
          break;

        // Case 'x': Set the expression to multiplication ('*').
        case 'x':
          this.expression[1] = '*';
          break;

        // Case '%': Set the expression to division ('/').
        case '%':
          this.expression[1] = '/';
          break;
      }

      // Update the formula with the current input value, excluding 'AC' and '='.
      this.formula = value === 'AC' || value === '=' ? this.formula : `${this.formula}${value}`;

      // Set the current value to 1.
      this.currentValue = 1;

      // Determine whether to reset the screen value based on the input value.
      this.resetScreenValue = !(value === 'AC' || value === '=');

      // Check if the input value is not 'AC' or '=' and the second numeric value is present.
      if (value !== 'AC' && value !== '=' && this.numericValues[1]) {
        // Use the eval function to perform the calculation based on the stored expression.
        this.numericValues[0] = eval(`${this.numericValues[0]} ${this.expression[0]} ${this.numericValues[1]}`);

        // Reset the second numeric value to null.
        this.numericValues[1] = null;

        // Convert the first numeric value in the array to a string and assign it to screenValue
        this.screenValue = this.numericValues[0].toString();
      }
    },
    evaluateExpression() {
      // Check if both numeric values at indices 0 and 1 are present and not null.
      if (this.numericValues[0] && this.numericValues[1]) {
        // Use the eval function to perform the calculation based on the stored expression.
        const result = eval(`${this.numericValues[0]} ${this.expression[0]} ${this.numericValues[1]}`);

        // Update the screen value to display the calculated result as a string.
        this.screenValue = result.toString();

        // Update the first numeric value with the calculated result, and reset the second numeric value.
        this.numericValues[0] = this.screenValue;
        this.numericValues[1] = null;

        // Append the calculated result to the existing formula, separated by '='.
        this.formula = this.formula + '=' + this.screenValue;
      }
    },
    clearValues() {
      // Retrieve the initial values from a function called getInitialValues().
      const initialValues = getInitialValues();
      // Iterate over each key in the initial values.
      Object.keys(initialValues).forEach(key => {
        // Assign the initial value of each key to the corresponding property of the current object.
        this[key] = initialValues[key];
      });
    }
  },
};
</script>
  
<style lang="scss">
.calculator {
  height: 330px;
  width: 390px;
  border-radius: 10px;
  overflow: hidden;
  background-color: black;
}

.grid {
  display: grid;
  grid-template-columns: 200px 115px;
  align-items: center;
  justify-content: center;
  gap: 14px;
}

.buttons {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 15px;
  height: 100%;

  .button-item:not(.equals-button) {
    height: 40px;
    width: 40px;
    box-sizing: border-box;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  &.grid-2-columns {
    grid-template-columns: repeat(2, 50px);
  }

  .number-button {
    font-size: 25px;
  }

  .equals-button {
    grid-row: span 2;
    border-radius: 10px;
    background-color: #FF513C;
  }
}
</style>