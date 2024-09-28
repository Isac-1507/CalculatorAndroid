<script lang="ts">
  import { onMount } from 'svelte'

  // Variable to store the number to be displayed
  let display_number = ''

  // Function to append a selected number to the display_number
  // eslint-disable-next-line @typescript-eslint/explicit-function-return-type
  const select = (num) => () => (display_number += num)

  // Function to clear the display_number
  // eslint-disable-next-line @typescript-eslint/explicit-function-return-type
  const clear = () => (display_number = '')

  const backspace = () => (display_number = display_number.slice(0, -1))
  // Variables to store the operand, result, and operator
  let operand
  let result
  let operator

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

<body style="background-color: #323232;">
  <div class="container">
    <div class="display">
      {display_number.length < 23 ? display_number : display_number.substring(0, 23)}
    </div>
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
</body>

<style>
  .container {
    display: inline-block;
    border: 1px solid #ccc;
    padding: 10px;
    border-radius: 5px;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
    background: #323232;
  }

  .display {
    font-size: 24px;
    border: 1px solid #ccc;
    height: 24px;
    padding: 10px;
    margin-bottom: 10px;
    width: 300px;
    text-align: right;
    border-radius: 5px;
    font-family: Arial, Helvetica, sans-serif;
    color: #ccc;
  }
  .buttons {
    display: grid;
    grid-template-columns: repeat(4, 75px);
    gap: 5px;
  }
  button {
    padding: 10px;
    font-size: 18px;
    border: none;
    cursor: pointer;
    border-radius: 5px;
    font-family: Arial, Helvetica, sans-serif;
    text-align: center;
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
</style>
