# About keyscript:
- designed for simplicity with a syntax similar to C.
- It features a static type system and is compiled to WebAssembly functions that can run on every browser.
- These functions, when called from JavaScript, offer superior performance, especially for computation-intensive tasks.

# How to get started with keyscript:
- Check out the Keyscript website for repo, issues, docs, updates and more: `https://keyscript.org`
- Go to the GitHub repo to install keyscript: `https://github.com/keyscript/keyscript`
- Install the keyscript version that matches your machine: `https://github.com/keyscript/keyscript/releases/tag/v0.1.0`
- Go to the directory of the installed release.

## For Windows:
- Run `./keyscript.exe init` to generate the starter files.
- Run `./keyscript.exe ./file.kys` to compile a Keyscript file.
- Run `./keyscript.exe ./file.kys debug` to compile the file and generate a readable .wat file.
- Run `./keyscript.exe ./file.kys gen` to compile the file and generate the necessary JS code to import the functions automatically.
- You can also add `gen` after `debug` for JS code generation.
  
## For Linux and Mac:
- Run `./keyscript init` to generate the starter files.
- Run `./keyscript ./file.kys` to compile a Keyscript file.
- Run `./keyscript ./file.kys debug` to compile the file and generate a readable .wat file.
- Run `./keyscript ./file.kys gen` to compile the file and generate the necessary JS code to import the functions automatically.
- You can also add `gen` after `debug` for JS code generation.

## Using Keyscript with html:
-To use Keyscript, you need to host the html file containing the JS code.
- When running `./keyscript init`, you will also generate the `index.html` file.
- Or when running `./keyscript ./file.kys gen`, you will generate the `file.html` containing the necessary JS code for importing all the keyscript functions you created.
- You can use the vscode extension `live server` to host the html file easily.

# Using Your KeyScript Code with JavaScript
Keyscript is a language that compiles to WebAssembly. To use your keyscript code, you need to import the functions into JavaScript and use them there.

## Importing KeyScript Functions into JavaScript
There are three ways to import KeyScript functions into JavaScript:
1. Using the `./keyscript ./file.kys gen` Command:
    - This command generates a `file.html` file containing the necessary JavaScript code for importing all the Keyscript functions you created.

2. Importing Using a JavaScript Promise:
   - First, make sure to include the following JavaScript code:
   ```javascript
   let imports = {
        wasm: {
            memory: new WebAssembly.Memory({initial: 256}), // 1 page = 64KB, 256 pages = much storage
        },
        console: {
            log: function (offset, length) {
                console.log(new TextDecoder('utf8').decode(new Uint8Array(imports.wasm.memory.buffer, offset, length)));
            }
        }
   };
   ```
    - Then, use the following code to import the Keyscript functions:
    ```javascript
    fetch('index.wasm') // file name!!
        .then(response => response.arrayBuffer())
        .then(bytes => {
            return WebAssembly.instantiate(bytes, imports)
        })
        .then(result => {
            const returnValue = result.instance.exports.main(); // import functions here
            if (returnValue) {
                document.getElementById('output').textContent = `Function returned: ${returnValue}`;
            } else {
                document.getElementById('output').textContent = `No output, check the console`;
            }

        })
        .catch(error => {
            document.getElementById('error').textContent = `Error loading WebAssembly: ${error.message}`;
        })
    ```
   - The provided file also contains a bit of error handling and output representation.
   - To import a function, use `func = result.instance.exports.function_name`. you can now use `func` as a normal JavaScript function, by calling it with `func(params)`.
3. Importing inside a javascript async function:
   - First, make sure to include the following JavaScript code:
   ```javascript
   let imports = {
        wasm: {
            memory: new WebAssembly.Memory({initial: 256}), // 1 page = 64KB, 256 pages = much storage
        },
        console: {
            log: function (offset, length) {
                console.log(new TextDecoder('utf8').decode(new Uint8Array(imports.wasm.memory.buffer, offset, length)));
            }
        }
   };
   ```
    - Then, use the following code to import the Keyscript functions:
    ```javascript
    (async () => {
        const response = await fetch('index.wasm'); // file name!!
        const bytes = await response.arrayBuffer();
        const result = await WebAssembly.instantiate(bytes, imports);
        const main_func = result.instance.exports.main; // import functions here
    })();
    ```
   - To import a function, use `func = result.instance.exports.function_name`. you can now use `func` as a normal JavaScript function, by calling it with `func(params)`.

# keyscript syntax

- Keyscript starts from the first line, that is Keyscript's main function.
- Keyscript's types: `bool`, `int`, `float`, `string`
- Variable declaration: Use `int identifier = value;` syntax, variables do not require an initial value.
- Control flow: Keyscript uses the `<`, `>`, `<=`, `>=`, `==`, `&&`, `||` operators for control flow.
- Arithmetic operations: Keyscript uses `+`, `-`, `*`, `/`, `%`, `+=`, `-=`, `*=`, `/=` for basic arithmetic operations.
- If statement:
  `if boolean_expression {
  code
  } else {
  code
  }`
- Loops:
- While loop: `while boolean_expression { code }`
- I/O: keyscript uses `print()` for output, use JS for input.
- Keyscript also allows string concatenation `"hi" + " " + "there"` would be `hi there`.
- Functions: Keyscript uses the return type with a function name and (parameters) syntax.
- Functions can either return: `bool`, `int`, `float`, `void` (no return type)
- Example of Keyscript's syntax:
```C
int add(int a, int b) {
  return a + b;
}
```
