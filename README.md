#### Notes

Please have a look the following file for code snippets/samples

#### [src/main.rs](https://github.com/giridharmb/rust-app1/blob/master/src/main.rs)

How To Create A New Cargo Project (Executable App) ?

```bash
cargo new test_app --bin
```

How To Run The Application ?

```bash
cargo run
```

How To Run The Unit Tests ?

```bash
cargo test
```

```rust
// In this print statement >
println!("{:?}", my_data);

{:?} -> is debug flag/trait
{:#?} -> is pretty debug flag/trait

// To apply debug flag/trait, we need to add this >

#[derive(Debug)] -> this is an annotation
```

`ownership` | `borrowing` | `references`

Important : Use of Reference

```rust
struct MyObject {
    width: u32,
    height: u32
}

let my_object = MyObject {
    width:15,
    height:20
};

// If you calculate area this way >

fn calculate_area(obj: MyObject) -> u32 {
    obj.width * obj.height
}

calculate_area(my_object); // -> this will result in error !

// You will see an error in the compiler this way:

// error[E0505]: cannot move out of `my_object` because it is borrowed
// move out of `my_object` occurs here

// Instead, you should calculate the area this way:

fn calculate_area(obj: &MyObject) -> u32 { // note that it is (obj: &MyObject)
    obj.width * obj.height
}

calculate_area(&my_object); // this will work as expected
```

Only one reference can own one peice of data at any given time.

```rust
let s = String::from("hello");
let y = s;
println!("{}", s); // -> this is NOT VALID , since y owns s now
```

To fix it

```rust
let s = String::from("hello");
let y = &s; // this is called "borrowing" , y is borrowing reference of s
println!("{}", s); // -> now, this is perfectly fine
```

HEAP (for complex data types)

```rust
fn take_data(v: Vec<i32>) {
    println!("We took v : sum of v[0] + v[1] is equal to : {}", v[0] + v[1]);
}

fn main() {
    let mut v = Vec::new();

    for i in 1..100 {
        v.push(i);
    }

    take_data(v); // this is called 'moving'

    // take_data(...)
    // above this peice of code, will transfer the
    // ownership of 'v' from main() function to take_data() function

    // println!("We took v : sum of v[0] + v[1] is equal to : {}", v[0] + v[1]);

    println("finished !");
}
```

Copying

```rust
fn copy_data(a: i32, b: i32) {
    println!("{}", a+b);
}

fn main() {
    let a = 32;
    let b = 45;

    // a and b -> exist on the stack, and not the heap,
    // so in the below call function call 'copy_data(a,b)'
    // they are 'copied' and not 'moved'
    // so, they will still remain in the scope of main()

    copy_data(a,b);

    println!("we have a: {} , b: {}", a,b);
}
```

Borrowing

- borrowing lets us have multiple references to a single resource
- a reference is also an object in rust
- `mutable` references -> are `MOVED`
- `immutable` references -> are `COPIED`

<hr/>

`mutable` vs `immutable` references

```rust
fn re(v: Vec<i32>) -> Vec<i32> {
    println!("{}", v[15] + v[25]);
    return v
}

fn borrow_1(v: &Vec<i32>) {
    println!("{}", (*v)[10] + (*v)[20]); // note : this is '(*v)' and not 'v'
}

fn borrow_2(v: &Vec<i32>) {
    println!("{}", v[30] + v[40]); // note : this is 'v' and not '(*v)'
}

fn main() {
    let mut v = Vec::new();

    for i in 1..1000 {
        v.push(i);
    }

    v = re(v); // re() function will transfer ownership of 'v' back to main() function

    println!("still own v : {} {}", v[0], v[1]);

    borrow_1(&v);
    println!("still own v : {} {}", v[0], v[1]);

    borrow_2(&v);
    println!("still own v : {} {}", v[0], v[1]);
}
```

Result Type : Is of type `enum`

```rust
let mut my_input_string = String::new();

println!("enter a number: ");

io::stdout().flush().unwrap();

// method-1 (not safe)
// completely commented out

/*
unwrap() will extract data from Result
unwrap() : This is not safe to do : user can input text instead of numbers == crash
*/

// method_1_start
{
    stdin().read_line(&mut my_input_string).expect("did not enter valid input !");
    let my_number: f64 = my_input_string.trim().parse().unwrap();
    let my_number: f64 = my_input_string.trim().parse().expect("invalid input, enter a number !");
    println!("Yay ! you entered a number : {:?}", my_number);
}
// method_1_end


// method-2 (safe) -> use it this way !

// what ever value loop returns, that will be stored in my_num
// whatever value comes after 'break', that value will be returned by loop

// method_2_start
let my_num = loop {
    my_input_string.clear();

    stdin().read_line(&mut my_input_string).expect("did not enter a correct string !");

    match my_input_string.trim().parse::<f64>() {
        Ok(_s) => {
            break _s;
        }
        Err(_err) => {
            println!("Try again, invalid input string.");
        }
    }
};
println!("Yay ! you entered a number : {:?}", my_num);
// method_2_end
```

Result -> enum : it represents success or failure

```rust
enum Result<T, E>
{
    Ok(T),
    Err(E),
}
```

Rust's named lifetimes

```
Every reference in Rust has a lifetime, which is the scope for which that 
reference is valid. Most of the time lifetimes are implicit and inferred, 
just like most of the time types are inferred. 
Similarly to when we have to annotate types because multiple types are 
possible, there are cases where the lifetimes of references could be related 
in a few different ways, so Rust needs us to annotate the relationships 
using generic lifetime parameters so that it can make sure the actual 
references used at runtime will definitely be valid.

Lifetime annotations don’t change how long any of the references involved live. 
In the same way that functions can accept any type when the signature specifies a 
generic type parameter, functions can accept references with any lifetime when the 
signature specifies a generic lifetime parameter. What lifetime annotations do is 
relate the lifetimes of multiple references to each other.

Lifetime annotations have a slightly unusual syntax: the names of lifetime parameters 
must start with an apostrophe '. The names of lifetime parameters are usually all lowercase, 
and like generic types, their names are usually very short. 'a is the name most people 
use as a default. Lifetime parameter annotations go after the & of a reference, 
and a space separates the lifetime annotation from the reference’s type.
```

```rust
pub fn func_test(a: &str, b: &str) -> &str {
    return b
}
```

Above function won't compile because it returns a borrowed value, <br/>
but does not specify whether it borrowed it from `a` or `b`.

`func_test(...)` throws the following error:

```
error[E0106]: missing lifetime specifier

XXX | pub fn func_test(a: &str, b: &str) -> &str {
    |                     ----     ----     ^ expected named lifetime parameter

help: this function's return type contains a borrowed value, but the signature
does not say whether it is borrowed from `a` or `b`

help: consider introducing a named lifetime parameter

XXX | pub fn func_test<'a>(a: &'a str, b: &'a str) -> &'a str {
    |                 ++++     ++          ++          ++
```

To understand it better , run this 

`rustc --explain E0106`

To fix the issue, you'd declare a named lifetime and use the same lifetime for `a` or `b` and the return type:

Replace the above function with this

```rust
pub fn func_test<'r>(a: &'r str, b: &'r str) -> &'r str {
    return b
}
```

And use it as expected

```rust
func_test("a", "b")
```

#### Option

The below code will not compile, because `id` can be anything apart from value of `1`

```rust
fn lookup_player(id: u32) -> String {
    if id == 1 {
        return "Giridhar Bhujanga".to_string()
    }
}
```

To fix this, we can use `Option`

```rust
fn lookup_player(id: u32) -> Option<String> {
    if id == 1 {
        let data = "Giridhar Bhujanga".to_string();
        return Option::Some(data);
    }
    return Option::None;
}
```

This function return signature : `lookup_player(id: u32) -> Option<String>` <br/>
means that we either return some `String` or `None`.

We can simplify it further by using `enum`:

```rust
fn lookup_player(id: u32) -> Option<String> {
    use Option::Some;
    use Option::None;

    if id == 1 {
        let data = "Giridhar Bhujanga".to_string();
        return Some(data);
    }
    return None;
}
```

Now we can call the above function `lookup_player`:

```rust
fn run_game() {
    let player = match lookup_player(1) {
        Some(p) => p,
        None => return
    };
}
```

FYI : An `empty tuple` is `()`, is also called a `unit`.

```rust
fn run_game() -> Option<()> {
    let player = lookup_player(1)?;
    println!("Player : {}", player);
    Some(())
}
```

Note : `?` above, that is <br/>
`lookup_player(1)?` : This will return `None`, If `lookup_player(1)` returns `None`.

#### Error Handling

Error Handling : Example-1

```rust
// Error Handling

use std::fs::File;

fn main() {
    println!("this is an example of error handling");
    let f = File::open("random.txt");

    let f = match f {
        Ok(file) => file,
        Err(err) => panic!("there was an error opening the file : {:?}", err), 
    };
}
```

In the above example, there was an error and output of `cargo run` was

```bash
cargo run
```

```bash
    Finished dev [unoptimized + debuginfo] target(s) in 0.19s
     Running `target/debug/test_app`
this is an example of error handling
thread 'main' panicked at 'there was an error opening the file : Os { code: 2, kind: NotFound, message: "No such file or directory" }', src/main.rs:11:21
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

```bash
RUST_BACKTRACE=1 cargo run
```

```bash
this is an example of error handling
thread 'main' panicked at 'there was an error opening the file : Os { code: 2, kind: NotFound, message: "No such file or directory" }', src/main.rs:11:21
stack backtrace:
   0: rust_begin_unwind
             at /rustc/69f9c33d71c871fc16ac445211281c6e7a340943/library/std/src/panicking.rs:575:5
   1: core::panicking::panic_fmt
             at /rustc/69f9c33d71c871fc16ac445211281c6e7a340943/library/core/src/panicking.rs:65:14
   2: test_app::main
             at ./src/main.rs:11:21
   3: core::ops::function::FnOnce::call_once
             at /rustc/69f9c33d71c871fc16ac445211281c6e7a340943/library/core/src/ops/function.rs:251:5
note: Some details are omitted, run with `RUST_BACKTRACE=full` for a verbose backtrace.
```

Error Handling : Example-2

```rust
// Error Handling

use std::fs::File;
use std::io::ErrorKind;

fn main() {
    println!("this is an example of error handling");
    let f = File::open("random.txt");

    let f = match f {
        Ok(file) => file,
        Err(error) => match error.Kind() {
            // recover from the error and try to create the file
            // also note, trying to create the file could also fail
            ErrorKind::NotFound => match File::create("random.txt") {
                Ok(fc) => fc, // just return fc if file could be created
                Err(e) => panic!("there was a problem in trying to create the file : {:?}", e),
            },
            other_error => {
                panic!("there was a different problem in opening the file : {:?}", other_error)
            }
        },
    };
}
```

Error Handling : Example-3

```rust
// Error Handling

use std::fs::File;
use std::io::ErrorKind;

fn main() {
    println!("this is an example of error handling");
    let f = File::open("random.txt").unwrap_or_else(|error| {
        if error.kind() == ErrorKind::NotFound {
            File::create("random.txt").unwrap_or_else(|error| {
                panic!("could not create the file : {:?}", error);
            }) // unwrap_or_else
        } else {
            panic!("could not open the file : {:?}", error);
        }
    }); //unwrap_or_else
}
```

Error Handling : Example-4

```rust
// Error Handling

use std::net::{AddrParseError, IpAddr};
use std::net::{Ipv4Addr};
use std::str::FromStr;


#[derive(Debug)]
pub enum GenericError {
    InvalidInput,
}

#[derive(Debug)]
struct CustomError {
    err_type: GenericError,
    err_msg: String
}

pub type IpCheckResult = Result<bool, CustomError>;

fn validate_ip_address(my_ip: &String) -> IpCheckResult {
    let ip_addr = Ipv4Addr::from_str(my_ip);
    let resp = match ip_addr {
        Ok(ip) => {
            println!("ip-address : {} , is valid !", my_ip);
            true
        },
        Err(e) => {
            println!("ip-address : {} , is *not* valid !", my_ip);
            let custom_err = CustomError{
                err_msg: String::from("there was an error in parsing the ipaddress"),
                err_type: GenericError::InvalidInput
            };
            return Err(custom_err)
        },
    };
    Ok(resp)
}

fn main() {
    println!("this is an example of error handling");

    // valid input
    let my_str = String::from("127.0.0.77");
    let _ = match validate_ip_address(&my_str) {
        Ok(data) => {
            println!("success : {:?}", data)
        }
        Err(e) => {
            println!("error : ip address validation failed : {:#?}", e)
        }
    };

    // invalid input
    let my_str = String::from("127.0.0.777");
    let _ = match validate_ip_address(&my_str) {
        Ok(data) => {
            println!("success : {:?}", data)
        }
        Err(e) => {
            println!("error : ip address validation failed : {:#?}", e)
        }
    };
}
```

Output of Example-4

```bash
cargo run
```
```bash
this is an example of error handling
ip-address : 127.0.0.77 , is valid !
success : true
ip-address : 127.0.0.777 , is *not* valid !
error : ip address validation failed : CustomError {
    err_type: InvalidInput,
    err_msg: "there was an error in parsing the ipaddress",
}
```

Error Handling : Example-5 (Without Error)

```rust
use std::collections::HashMap;
use reqwest::{Response, StatusCode};


fn main() {
    match get_current_date() {
        Ok(date) => println!("We've time travelled to {} !", date),
        Err(e) => eprintln!("Oh noes, we don't know which era we're in ! :( \n  {}", e),
    }
}

fn get_current_date() -> Result<String, reqwest::Error> {
    let url = "https://postman-echo.com/time/object";

    let client = reqwest::blocking::Client::new();

    let res = client.get(url).send();

    let response = match res {
        Ok(res) => res,
        Err(err) => return Err(err),
    };

    let body = response.json::<HashMap<String, i32>>();

    let json = match body {
        Ok(json) => json,
        Err(err) => return Err(err),
    };

    let date = json["years"].to_string();

    Ok(date)
}
```

Output of Example-5 (Without Error)

```bash
    Finished dev [unoptimized + debuginfo] target(s) in 0.31s
     Running `target/debug/test_app_v2`
We've time travelled to 2023 !
```

Error Handling : Example-5 (*With* Error , Invalid URL Passed)

```rust
use std::collections::HashMap;
use reqwest::{Response, StatusCode};


fn main() {
    match get_current_date() {
        Ok(date) => println!("We've time travelled to {} !", date),
        Err(e) => eprintln!("Oh noes, we don't know which era we're in ! :( \n  {}", e),
    }
}

fn get_current_date() -> Result<String, reqwest::Error> {
    let url = "https://postman-echo.com/time/object-XXX";

    let client = reqwest::blocking::Client::new();

    let res = client.get(url).send();

    let response = match res {
        Ok(res) => res,
        Err(err) => return Err(err),
    };

    let body = response.json::<HashMap<String, i32>>();

    let json = match body {
        Ok(json) => json,
        Err(err) => return Err(err),
    };

    let date = json["years"].to_string();

    Ok(date)
}
```

Output of Example-5 (*With* Error , Invalid URL Passed)

```bash
    Finished dev [unoptimized + debuginfo] target(s) in 0.31s
     Running `target/debug/test_app_v2`
Oh noes, we don't know which era we're in ! :(
  error decoding response body: EOF while parsing a value at line 1 column 0
```
