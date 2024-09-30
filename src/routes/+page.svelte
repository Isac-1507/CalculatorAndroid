<script lang="ts">
  import { onMount } from 'svelte'
  import { fit, parent_style } from '@leveluptuts/svelte-fit'
  // Variable to store the number to be displayed
  let display_number = ''

  // Function to append a selected number to the display_number
  // eslint-disable-next-line @typescript-eslint/explicit-function-return-type
  const select = (num) => () => (display_number += num)

  // Function to clear the display_number
  // eslint-disable-next-line @typescript-eslint/explicit-function-return-type

  const backspace = () => (display_number = display_number.slice(0, -1))
  // Variables to store the operand, result, and operator
  let operand
  let result
  let operator

  const clear = () => (display_number = '', result = null, operand = null, operator = null )
  // Array of valid operators
  let operators = ['+', '-', '*', '/', 'x']

  // Function to handle keydown events
  function handleKeydown(event: KeyboardEvent): void {
    const key = event.key
    // If the key is a number, append it to the display_number
    if (!isNaN(Number(key)) || key === '.') {
      select(key.toString())()
      // If the key is an operator, perform the operation
    } else if (operators.includes(key)) {
      operation(key)
      // If the key is Enter, calculate the result
    } else if (key === 'Enter') {
      equals()
      // If the key is Escape, clear the display_number
    } else if (key === 'Escape') {
      clear()
    } else if (key === 'Backspace') {
      backspace()
    }
  }

  // Function to store the operand and operator, and clear the display_number
  function operation(sign): void {
    operand = Number(display_number)
    operator = sign
    display_number += ` ${sign} `
  }

  // Function to calculate the result based on the operator and operand
  function equals(): void {
    if (operator !== null && operand !== null && display_number !== '') {
      const currentNum = Number(display_number.split(' ').pop())
      switch (operator) {
        case '+':
          result = operand + currentNum
          break
        case '-':
          result = operand - currentNum
          break
        case 'x':
        case '*':
          result = operand * currentNum
          break
        case '/':
          result = operand / currentNum
          break
      }
    }
    display_number = result.toString()
  }

  // Lifecycle function to add and remove the keydown event listener
  onMount(() => {
    window.addEventListener('keydown', handleKeydown)
    return (): void => {
      window.removeEventListener('keydown', handleKeydown)
    }
  })
  onMount(() => {
    const buttons = document.querySelectorAll('button')

    buttons.forEach((button) => {
      button.addEventListener('keydown', (event) => {
        if (event.key === 'Enter') {
          event.preventDefault()
        }
      })
    })
  })
</script>
<!-- svelte-ignore a11y-missing-attribute -->
<html>
<body style="background-color: #323232;">
  <div class="container">
    <div class="display" use:fit={{max_size:50}}>
      {display_number.length < 12 ? display_number : display_number.substring(0, 12)}
    </div>
    <div class="buttons-container">
    <div class="buttons">
      <button on:click={select(7)}>7</button>
      <button on:click={select(8)}>8</button>
      <button on:click={select(9)}>9</button>
      <button class="operator" on:click={() => operation(operators[0])}>+</button>
      <button on:click={select(4)}>4</button>
      <button on:click={select(5)}>5</button>
      <button on:click={select(6)}>6</button>
      <button class="operator" on:click={() => operation(operators[1])}>-</button>
      <button on:click={select(1)}>1</button>
      <button on:click={select(2)}>2</button>
      <button on:click={select(3)}>3</button>
      <button class="operator" on:click={() => operation(operators[2])}>*</button>
      <button on:click={select(0)}>0</button>
      <button on:click={select('.')}>.</button>
      <button on:click={clear} class="clear">C</button>
      <button class="operator" on:click={() => operation(operators[3])}>/</button>
      <button class="equals" on:click={equals}>=</button>
      <!--Spacer-->
      <div class="spacer"></div>
      <button on:click={backspace} class="clear">←</button>
    </div>
    </div>
  </div>
</body>
</html>
<style>
html, body {
  margin: 0;
  padding: 0;
  height: 100%;
  width: 100%;
  overflow: hidden; /* Prevent scrolling */
  background-color: #323232; /* Set the background color */
}

.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start; /* Align items to the top */
  width: 100vw; /* Make the container take the full width of the viewport */
  height: 100vh; /* Make the container take the full height of the viewport */
  box-sizing: border-box; /* Include padding and border in the element's total width */
}

.display {
  container-name: display;
  background-color: #000;
  margin-top: auto;
  margin-bottom: 0%;
  font-size: auto;
  border: 1px solid #ccc;
  height: 10%; /* Fixed height for the display */
  padding: 10px;
  width: 95%; /* Make the display take the full width of the container */
  text-align: right;
  border-radius: 5px;
  font-family: Arial, Helvetica, sans-serif;
  color: #ccc;
  box-sizing: border-box; /* Include padding and border in the element's total width */
  display: flex;
  align-items: center; /* Center text vertically */
  justify-content: flex-end; /* Align text to the right */
}

.buttons-container {
  width: 100%; /* Make the buttons container take the full width of the container */
  height: 50%; /* Fixed height for the buttons area */
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: auto;
  margin-bottom: auto;
}

.buttons {
  display: grid;
  grid-template-columns: repeat(4, 1fr); /* Each column will take an equal fraction of the available space */
  gap: 5px;
  width: 95%; /* Make the grid take the full width of the buttons container */
  height: 95%; /* Make the grid take the full height of the buttons container */
  box-sizing: border-box; /* Include padding and border in the element's total width and height */
}

button {
  font-size: 18px;
  border: none;
  cursor: pointer;
  border-radius: 90px;
  font-family: Arial, Helvetica, sans-serif;
  text-align: center;
  aspect-ratio: 1 / 1; /* Ensure buttons are square */
  width: 100%; /* Ensure buttons take the full width of their grid cell */
  height: 100%; /* Ensure buttons take the full height of their grid cell */
}

.operator {
  background: #878787;
  border: 1px solid #000;
}

.equals {
  background: #e7a932;
}

.clear {
  border: 0px solid #000;
  background: red;
}

.operator:hover {
  background: #adadad;
}

.clear:hover {
  background: #ffc0cb;
}

.equals:hover {
  background: #ffd700;
}

.spacer {
  padding: 10px;
  border: none;
  border-radius: 5px;
}

/* Media query for smaller screens */
@media (max-width: 600px) {
  .display {
    font-size: 18px; /* Adjust font size for smaller screens */
  }

  button {
    font-size: 16px; /* Adjust font size for smaller screens */
    padding: 8px; /* Adjust padding for smaller screens */
  }
}
</style>