# Complex rust examples

## File I/O and stdout

- Use `File::open` to open input files
- Use `File::create` to create (or truncate) output files
- `BufReader` allows for efficient reading and `BufWriter` allows for efficient writing
- The `?` operator propogates errors, returning an `Err` variant and terminating the program

```rust
use std::fs::File; 
use std::io::{self, BufRead, BufReader, Write}; 

fn main() -> io::Result<()> { 
    // Open the input file for reading using BufReader 
    let file = File::open("input.txt")?; 
    let reader = BufReader::new(file);

    // Read lines from the input file and process them 
    for line in reader.lines() { 
        let input_line = line?; 
        let number: i32 = input_line.trim().parse().expect("Invalid number in file"); 

        // Perform a simple operation (multiply by 2 in this case) 
        let result = number * 2; 

        // Print the result to stdout 
        println!("Input: {}, Output: {}", number, result); 

        // Open the output file for writing 
        let mut output_file = File::create("output.txt")?; 

        // Write the result to the output file 
        writeln!(output_file, "{}", result)?; 
    } 

    Ok(()) 
}
```

## Fibonacci sequence

- `Vec<T>` can be used to create a dynamic array of elements of type `T`
- Ranges can be used to iterate over a specifed range of numbers

```rust
// Declare a function to calculate the nth Fibonacci number using dynamic programming. 
fn fibonacci_dynamic(n: u64) -> u64 { 
    // Create a vector to store the Fibonacci numbers. 
    let mut fib_nums: Vec<u64> = Vec::with_capacity(n as usize + 1); 

    // Initialize the first two Fibonacci numbers. 
    fib_nums.push(0); 
    fib_nums.push(1); 

    // Calculate and store Fibonacci numbers from the 3rd to the nth. 
    for i in 2..=n { 
        let next_fib = fib_nums[i as usize - 1] + fib_nums[i as usize - 2]; 
        fib_nums.push(next_fib); 
    } 

    // Return the nth Fibonacci number. 
    fib_nums[n as usize] 
} 

fn main() { 
    // Define the value of n for which we want to calculate the Fibonacci number. 
    let n: u64 = 10; 

    // Call the fibonacci_dynamic function and store the result. 
    let result = fibonacci_dynamic(n); 

    // Print the result. 
    println!("The {}th Fibonacci number is: {}", n, result); 
}
```

## Device drive code example 

- The `embedde_hal` and `cortex_m::asm` are necessary libraries for working with embedded systems
- The `GpioController` struct represents a GPIO controller 
- The `MyGpioPin` struct represents a single GPIO pin

```rust
// Import the necessary library for interacting with hardware registers 
// embedded_hal is a library that provides a common hardware abstraction layer for embedded systems 
use embedded_hal::digital::v2::OutputPin; 
use cortex_m::asm; // Import the ARM Cortex-M assembly macros for delaying 

// Define a struct to represent the GPIO controller 
pub struct GpioController { 
    pin_a: MyGpioPin, 
    pin_b: MyGpioPin, 
    pin_c: MyGpioPin, 
} 

// Define a struct to represent an individual GPIO pin 
pub struct MyGpioPin { 
    // Add any necessary fields here to store the pin configuration and state 
    // For simplicity, we'll just use a boolean to represent the pin state (on/off) 
    is_on: bool, 
} 

impl GpioController { 
    // Constructor to create a new instance of the GPIO controller 
    pub fn new() -> GpioController { 
        // Initialize the individual GPIO pins here (this is just for illustration) 
        let pin_a = MyGpioPin { is_on: false }; 
        let pin_b = MyGpioPin { is_on: false }; 
        let pin_c = MyGpioPin { is_on: false }; 

        GpioController { pin_a, pin_b, pin_c } 
    } 

    // Function to turn on a specific LED (GPIO pin) 
    pub fn turn_on_led(&mut self, led: char) { 
        match led { 
            'A' => self.pin_a.set_high(), 
            'B' => self.pin_b.set_high(), 
            'C' => self.pin_c.set_high(), 
            _ => (), 
        } 
    } 

    // Function to turn off a specific LED (GPIO pin) 
    pub fn turn_off_led(&mut self, led: char) { 
        match led { 
            'A' => self.pin_a.set_low(), 
            'B' => self.pin_b.set_low(), 
            'C' => self.pin_c.set_low(), 
            _ => (), 
        } 
    } 
} 

// Implement the OutputPin trait for our custom GpioPin struct 
impl OutputPin for MyGpioPin { 
    type Error = (); 

    fn set_high(&mut self) -> Result<(), Self::Error> { 
        // Simulate the hardware behavior by changing the state of the GPIO pin 
        self.is_on = true; 
        // For a real implementation, you would write to the hardware register to set the pin high 
        // For demonstration purposes, we'll just print the action for now 
        println!("Set GPIO pin high"); 
        Ok(()) 
    } 

    fn set_low(&mut self) -> Result<(), Self::Error> { 
        // Simulate the hardware behavior by changing the state of the GPIO pin 
        self.is_on = false; 
        // For a real implementation, you would write to the hardware register to set the pin low 
        // For demonstration purposes, we'll just print the action for now 
        println!("Set GPIO pin low"); 
        Ok(()) 
    } 
} 

fn main() { 
    // Create a new instance of the GPIO controller 
    let mut gpio_controller = GpioController::new(); 

    // Turn on LED 'A' 
    gpio_controller.turn_on_led('A'); 
    // Wait for some time (simulate LED being on) 
    asm::delay(1000000); 

    // Turn off LED 'A' 
    gpio_controller.turn_off_led('A'); 
    // Wait for some time (simulate LED being off) 
    asm::delay(1000000); 
}
```

## User input example

- The `Car` struct contains fields for its details and a method to display the car's details
- Use `io::stdin()` to read input from the user 
    - The `readline()` method reads a line from stdin
    - The `parse()` method coverts the input into the appropriate data type

```rust
use std::io; 

// Define the Car struct 
struct Car { 
    make: String, 
    model: String, 
    year: u16, 
    color: String, 
    mileage: u32, 
} 

impl Car { 
    // Method to display car details 
    fn display(&self) { 
        println!( 
            "Make: {}\nModel: {}\nYear: {}\nColor: {}\nMileage: {} miles",              self.make, self.model, self.year, self.color, self.mileage 
        ); 
    } 
} 

fn main() { 
    // Get car details from the user 
    let car1 = get_car_details(); 
    let car2 = get_car_details(); 

    // Display car details 
    println!("Car 1:"); 
    car1.display(); 

    println!("Car 2:"); 
    car2.display(); 
} 

fn get_car_details() -> Car { 
    println!("Enter car details:"); 

    // Get input from the user 
    let mut input = String::new(); 

    // Make 
    println!("Make:"); 
    io::stdin().read_line(&mut input).expect("Failed to read input"); 
    let make = input.trim().to_string(); 
    input.clear(); 

    // Model 
    println!("Model:"); 
    io::stdin().read_line(&mut input).expect("Failed to read input"); 
    let model = input.trim().to_string(); 
    input.clear(); 

    // Year 
    println!("Year:"); 
    io::stdin().read_line(&mut input).expect("Failed to read input"); 
    let year: u16 = input.trim().parse().expect("Invalid input"); 
    input.clear(); 

    // Color 
    println!("Color:"); 
    io::stdin().read_line(&mut input).expect("Failed to read input"); 
    let color = input.trim().to_string(); 
    input.clear(); 

    // Mileage 
    println!("Mileage (in miles):"); 
    io::stdin().read_line(&mut input).expect("Failed to read input"); 
    let mileage: u32 = input.trim().parse().expect("Invalid input"); 

    // Create and return the car instance 
    Car { 
        make, 
        model, 
        year, 
        color, 
        mileage, 
    } 
}
```

## More advanced language features

### Reading a CSV file

- A `Record` struct holds the data from a CSV line

```rust
use std::fs::File; 
use std::io::{self, BufRead}; 
use std::error::Error; 

// Define a struct to hold the data from the CSV file 
#[derive(Debug)] 
struct Record { 
    name: String, 
    age: u32, 
    score: f64, 
} 

impl Record { 
    // A method to create a new Record from a CSV line 
    fn from_csv_line(line: &str) -> Result<Record, Box<dyn Error>> { 
        let fields: Vec<&str> = line.split(',').collect(); 
        if fields.len() != 3 { 
            return Err("Invalid CSV line".into()); 
        } 

        let name = fields[0].to_string(); 
        let age = fields[1].parse::<u32>()?; 
        let score = fields[2].parse::<f64>()?; 

        Ok(Record { name, age, score }) 
    } 
} 

fn main() -> Result<(), Box<dyn Error>> { 
    let filename = "data.csv"; 
    let mut records: Vec<Record> = Vec::new(); 

    // Read the CSV file line by line and parse into Record struct 
    let file = File::open(filename)?; 
    for line in io::BufReader::new(file).lines() { 
        let line = line?; 
        let record = Record::from_csv_line(&line)?; 
        records.push(record); 
    } 

    // Process the data: calculate the average score 
    let total_score: f64 = records.iter().map(|r| r.score).sum(); 
    let average_score = total_score / records.len() as f64; 

    println!("Number of records: {}", records.len()); 
    println!("Average score: {:.2}", average_score); 

    Ok(()) 
}
```

### A pizza ordering system

- The `Pizza` and `Order` structs represent pizzas and orders with their associated attributes
- A menu in represented by a `HashMap` with the different types of pizzas available to order 

```rust
use std::collections::HashMap; 
use std::io::{self, Write}; 

// Define the Pizza struct with available pizza options and their prices 
#[derive(Debug, Clone)] 
struct Pizza { 
    name: String, 
    price: f64, 
} 

// Create a hashmap to store available pizza options 
fn create_pizza_menu() -> HashMap<String, Pizza> { 
    let mut pizza_menu = HashMap::new(); 
    pizza_menu.insert( 
        "Margherita".to_string(), 
        Pizza { 
            name: "Margherita".to_string(), 
            price: 9.99, 
        }, 
    ); 
    pizza_menu.insert( 
        "Pepperoni".to_string(), 
        Pizza { 
            name: "Pepperoni".to_string(), 
            price: 11.99, 
        }, 
    ); 
    pizza_menu.insert( 
        "Vegetarian".to_string(), 
        Pizza { 
            name: "Vegetarian".to_string(), 
            price: 10.99, 
        }, 
    ); 
    pizza_menu 
} 

// Define the Order struct to represent a pizza order 
#[derive(Debug)] 
struct Order { 
    pizza: String, 
    quantity: u32, 
} 

impl Order { 
    // Method to calculate the total price of an order 
    fn total_price(&self, pizza_menu: &HashMap<String, Pizza>) -> Option<f64> { 
        match pizza_menu.get(&self.pizza) { 
            Some(pizza) => Some(pizza.price * self.quantity as f64), 
            None => None, 
        } 
    } 
} 

fn main() { 
    let pizza_menu = create_pizza_menu(); 
    let mut orders: Vec<Order> = Vec::new(); 

    loop { 
        // Display the pizza menu to the user 
        println!("Available Pizzas:"); 
        for (_, pizza) in &pizza_menu { 
            println!("{} - ${:.2}", pizza.name, pizza.price); 
        } 

        // Get user input for pizza order 
        println!("Enter your pizza choice (or 'q' to quit):"); 
        let mut input = String::new(); 
        io::stdin() 
            .read_line(&mut input) 
            .expect("Failed to read input"); 
        let pizza_choice = input.trim().to_string(); 
        input.clear(); 

        if pizza_choice == "q" { 
            break; 
        } 

        // Get user input for quantity 
        println!("Enter quantity:"); 
        io::stdout().flush().unwrap(); 
        io::stdin() 
            .read_line(&mut input) 
            .expect("Failed to read input"); 
        let quantity: u32 = match input.trim().parse() { 
            Ok(qty) => qty, 
            Err(_) => { 
                println!("Invalid quantity. Please enter a valid number."); 
                continue; 
            } 
        };  
        input.clear(); 

        // Check if the chosen pizza exists in the menu 
        if !pizza_menu.contains_key(&pizza_choice) { 
            println!("Invalid pizza choice. Please select a pizza from the menu."); 
            continue; 
        }

        // Create and store the order 
        let order = Order { 
            pizza: pizza_choice.clone(), 
            quantity, 
        }; 
        orders.push(order); 
    }

    // Display the final order and total price 
    println!("Your order:"); 
    let mut total_cost = 0.0; 
    for order in &orders { 
        println!( 
            "{} - {} x ${:.2} each = ${:.2}", 
            order.pizza, 
            order.quantity, 
            pizza_menu[&order.pizza].price, 
            order.total_price(&pizza_menu).unwrap() 
        ); 
        total_cost += order.total_price(&pizza_menu).unwrap(); 
    } 
    println!("Total Cost: ${:.2}", total_cost); 
}
```
