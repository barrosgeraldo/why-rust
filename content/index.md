class: center
name: title
count: false

<img src="content/images/rust-logo-blk.svg" alt="Rust logo" width="250rem" height="auto">

# Why Rust?

.grey[Santiago Pastorino]

---

# About me

- Computer Science at Fing
- WyeWorks co-founder
- Ruby on Rails core alumni
- Started with Rust in 2017
- Member of Rust compiler NLL WG
- Rust Latam conference organizer
- Rust Montevideo Meetup organizer

<div style="height: 2rem"></div>

Contact:

- Twitter/Github: spastorino
- Email: spastorino@gmail.com

---

# Agenda

1. Systems programming
1. Use cases
1. Abstraction without overhead
1. Memory safety without garbage collection
1. Concurrency without data races
1. Great tooling

---

# Systems programming

Systems programming aims to produce software and software platforms which provide **services to other software**, are **performance constrained**, or both.
(e.g. Operating Systems, Browsers, Game Engines, etc)

The primary distinguishing characteristic of systems programming when compared to application programming is that application programming aims to produce software which **provides services to the user directly** (e.g. word processor)

System programming requires a great degree of **hardware awareness**.

---

# Programming languages

<img src="content/images/rust-meetup-languages-decision-1.png" alt="Desicions 1">

???

- Manual memory management vs GC
- Control vs Safety

---

# Programming languages

<img src="content/images/rust-meetup-languages-decision-2.png" alt="Desicions 2">

---

<img src="content/images/rust-logo-blk.svg" alt="Rust logo" width="250rem" height="auto" style="position: absolute; right: 0rem; margin-top: -2rem;">

# What is Rust?

- "New" systems programming language
  - Developed by Mozilla research, v1.0 released in 2015
- Multiparadigm 
  - Imperative, Structured, Functional, Concurrent, Generic, Compiled 
- Static strong Typing
  - Inference
- Emphasizing control, safety, and speed
- Free and open-source software, MIT License or Apache License 2.0
- Most loved programming language (2016, 2017 & 2018)

---

# Use cases

- Low level dev
  - Operating Systems
  - Browsers
  - Servers
  - etc, etc
- Address hot spots of your app (Ruby, JavaScript, Python, etc)
- WebAssembly
- Web APIs
- Networking, Blockchain
- Embedded, Microcontrollers, IoT
- Games
- Machine learning
- Cli

???

- WG Focus

---

class: center

# Who is using Rust?

<div>
  <img src="content/images/firefox.svg" alt="Firefox logo" width="150rem" height="auto">
  <img src="content/images/servo.png" alt="Firefox logo" width="150rem" height="auto">
</div>
<img src="content/images/rust-logo-blk.svg" alt="Rust logo" width="200rem" height="auto">

---

# Who is using Rust?

- **Amazon** - Building tools in Rust.
- **Atlassian** (makers of Jira) - Using Rust in the backend.
- **Dropbox** - Using Rust in both the frontend and backend.
- **Facebook** - Tools for source control.
- **Google** - As part of the Fuchsia project.
- **Microsoft** - Using Rust in part of their new Azure IoT work.
- **npm** - Using Rust in some of the npm core services.
- **Red Hat** - Creating a new storage system
- **Reddit** - Using Rust in its comment processing
- **Twitter** - As part of the build team support for Twitter.

You can see even more on the Friends of Rust that include other familiar names like **Baidu**, **Disney**, **Wire**, **Mozilla**, **Samsung**, **Cloudflare**, **Gnome**, **Chef**, **Canonical**, **Coursera**, **Tor** and more.

---

# Who is using Rust?

**Redox OS**: Most complete Rust OS, microkernel design

<img class="center" alt="Screenshot of Redox running a webbrowser and a file manager" src="content/images/redox-screenshot.png" style="margin-left: auto; margin-right: auto; width: auto; height: 25rem;">

---

<img src="content/images/rust-logo-blk.svg" alt="Rust logo" width="250rem" height="auto" style="position: absolute; right: 0rem; margin-top: -2rem;">

# Why use Rust?

- **Abstraction without overhead**
- Memory safety without garbage collection
- Concurrency without data races

???

- **Hack without fear**.

---

# Abstraction without overhead

- High-level code
  - Algebraic data types
  - Generics
  - Traits
  - Closures
  - High order functions
  - Higienic macros
  - Testing built in
- Low-level performance
  - Fast code
  - Low memory footprint
  - with zero cost (no GC, unboxed closures, monomorphization); no runtime
- Cross-lang interop

???

- Monomorphization generate specialized versions of generic functions
- Unboxed closures: expand to an anonymous type which is effectively a struct that contains the captured variables

---

# Zero-cost abstractions

```rust
enum Event {
    Load,
    KeyPress(char),
    Click { x: i64, y: i64 }
}

fn print_event(event: Event) {
    match event {
        Event::Load => println!("Loaded"),
        Event::KeyPress(c) => println!("Key {} pressed", c),
        Event::Click {x, y} => println!("Clicked at x={}, y={}", x, y),
    }
}
```

???

- Algebraic data types / sum types
- Pattern matching
- Hygienic Macros

---

# Zero-cost abstractions

```rust
fn is_whitespace(text: &str) -> bool {
    text.chars()
        .all(|c| c.is_whitespace())
}
```

```rust
fn load_images(paths: &[PathBuf]) -> Vec<Image> {
    paths.iter()
         .map(|path| Image::load(path))
         .collect()
}
```

---

# High-level code, low-level performance

Let see a **Ruby on Rails** method implemented in pure
**Ruby** and as a native extension in **C** and in **Rust**.

---

# Ruby blank?

```ruby
class String
  def blank?
    /\A[[:space:]]*\z/ == self
  end
end
```

---

# C blank?

```c
static VALUE
rb_str_blank_as(VALUE str)
{
  rb_encoding *enc;
  char *s, *e;

  enc = STR_ENC_GET(str);
  s = RSTRING_PTR(str);
  if (!s || RSTRING_LEN(str) == 0) return Qtrue;

  e = RSTRING_END(str);
  while (s < e) {
    int n;
    unsigned int cc = rb_enc_codepoint_len(s, e, &n, enc);

    switch (cc) {
      case 9:
      case 0xa:
      case 0xb:
```

---

# C blank? (part 2)

```c
      case 0xc:
      case 0xd:
      case 0x20:
      case 0x85:
      case 0xa0:
      case 0x1680:
      case 0x2000:
      case 0x2001:
      case 0x2002:
      case 0x2003:
      case 0x2004:
      case 0x2005:
      case 0x2006:
      case 0x2007:
      case 0x2008:
      case 0x2009:
      case 0x200a:
      case 0x2028:
```

---

# C blank? (part 3)

```c
      case 0x2029:
      case 0x202f:
      case 0x205f:
      case 0x3000:
#if ruby_version_before_2_2()
      case 0x180e:
#endif
          /* found */
          break;
      default:
          return Qfalse;
    }
    s += n;
  }
  return Qtrue;
}
```

---

# C blank? (part 4)

```c
static VALUE
rb_str_blank(VALUE str)
{
  rb_encoding *enc;
  char *s, *e;

  enc = STR_ENC_GET(str);
  s = RSTRING_PTR(str);
  if (!s || RSTRING_LEN(str) == 0) return Qtrue;

  e = RSTRING_END(str);
  while (s < e) {
    int n;
    unsigned int cc = rb_enc_codepoint_len(s, e, &n, enc);

    if (!rb_isspace(cc) && cc != 0) return Qfalse;
    s += n;
  }
  return Qtrue;
}
```

---

# Rust blank?

```rust
extern “C” fn fast_blank(buf: Buf) -> bool {
    buf.as_slice().chars().all(|c| c.is_whitespace())
}
```

---

# blank? performance

<img src="content/images/performance.png" alt="Performance">

---

<img src="content/images/rust-logo-blk.svg" alt="Rust logo" width="250rem" height="auto" style="position: absolute; right: 0rem; margin-top: -2rem;">

# Why use Rust?

- Abstraction without overhead
- **Memory safety without garbage collection**
- Concurrency without data races

---

# Memory safety without garbage collection

- No segmentation faults
- No double free
- No dangling pointers
- No iterator invalidation
- No buffer overflows
- No undefined behavior
- No null pointers
- No data races
- Guaranteed by Rust's ownership system at compile time

???

- Every malloc needs one free
- Array capacity is not checked
- Vulnerabilities caused by memory unsafety are still common

---

# Memory safety

<img alt="Buffer Overflow Vulnerabilities in Linux" src="content/images/Buffer Overflow Vulnerabilities in Linux.svg" style="margin-top: 0rem; margin-bottom: -2rem;">

<img src="content/images/Tux.svg" alt="Linux logo" width="120rem" height="auto" style="display:block; position: absolute; top: 1rem; right: 3rem;">

.grey[.small[Source: https://www.cvedetails.com/product/47/Linux-Linux-Kernel.html?vendor_id=33]]

???

- CVE = Common Vulnerabilities and Exposures

---

# Memory safety

<img alt="Linux CVEs in 2018" src="content/images/Linux CVEs in 2018.svg" style="margin-top: 0rem; margin-bottom: -2rem;">

<img src="content/images/Tux.svg" alt="Linux logo" width="120rem" height="auto" style="display:block; position: absolute; top: 1rem; right: 3rem;">

.grey[.smaller[Source: https://www.cvedetails.com/vulnerability-list/vendor_id-33/product_id-47/year-2018/Linux-Linux-Kernel.html]]


???

- CVE = Common Vulnerabilities and Exposures

---

# "Manual" memory management in Rust:

- Values **owned** by creator.
- Values **moved** via assignment.
- When final owner returns, **value is freed**.

All this feels invisible and prevents _double free_ errors and _memory leaks_.

???

- Move semantics / RAII
- Rust enforces the RAII discipline
- Variables can own resources

---

# Ownership

```rust
fn main() {
    let apple_1 = Apple::new();
    eat(apple_1); // Give ownership of the apple_1.
    eat(apple_1); // Error: apple_1 has been moved.
}

/// eat function takes ownership of the apple
fn eat(apple_2: Apple) {
}
```

---

# Ownership

<img src="content/images/rust-meetup-children-ownership-0r.jpg" alt="Ownership 0" width="300rem" height="auto" style="position: absolute; right: 3rem; margin-top: 0rem">

```rust
fn main() {
    let apple = Apple::new();
    let mut bag = Vec::new();
    bag.push(apple); // Give ownership
    bag.push(Apple::new());
    deliver(bag); // Give ownership of bag and it's contents
}

/// deliver function takes ownership
/// of the vector
fn deliver(bag: Vec<Apple>) {
    // ...
}
```

---

# Ownership

<img src="content/images/rust-meetup-children-ownership-1r.png" alt="Ownership 1" width="300rem" height="auto" style="position: absolute; right: 3rem; margin-top: 0rem">

```rust
fn main() {
*   let apple = Apple::new();
    let mut bag = Vec::new();
    bag.push(apple); // Give ownership
    bag.push(Apple::new());
    deliver(bag); // Give ownership of bag and it's contents
}

/// deliver function takes ownership
/// of the vector
fn deliver(bag: Vec<Apple>) {
    // ...
}
```

---

# Ownership

<img src="content/images/rust-meetup-children-ownership-2r.png" alt="Ownership 2" width="300rem" height="auto" style="position: absolute; right: 3rem; margin-top: 0rem">

```rust
fn main() {
    let apple = Apple::new();
*   let mut bag = Vec::new();
    bag.push(apple); // Give ownership
    bag.push(Apple::new());
    deliver(bag); // Give ownership of bag and it's contents
}

/// deliver function takes ownership
/// of the vector
fn deliver(bag: Vec<Apple>) {
    // ...
}
```

---

# Ownership

<img src="content/images/rust-meetup-children-ownership-3r.png" alt="Ownership 3" width="300rem" height="auto" style="position: absolute; right: 3rem; margin-top: 0rem">

```rust
fn main() {
    let apple = Apple::new();
    let mut bag = Vec::new();
*   bag.push(apple); // Give ownership
    bag.push(Apple::new());
    deliver(bag); // Give ownership of bag and it's contents
}

/// deliver function takes ownership
/// of the vector
fn deliver(bag: Vec<Apple>) {
    // ...
}
```

---

# Ownership

<img src="content/images/rust-meetup-children-ownership-4r.png" alt="Ownership 4" width="300rem" height="auto" style="position: absolute; right: 3rem; margin-top: 0rem">

```rust
fn main() {
    let apple = Apple::new();
    let mut bag = Vec::new();
    bag.push(apple); // Give ownership
*   bag.push(Apple::new());
    deliver(bag); // Give ownership of bag and it's contents
}

/// deliver function takes ownership
/// of the vector
fn deliver(bag: Vec<Apple>) {
    // ...
}
```

---

# Ownership

<img src="content/images/rust-meetup-children-ownership-6r.png" alt="Ownership 6" width="300rem" height="auto" style="position: absolute; right: 3rem; margin-top: 0rem">

```rust
fn main() {
    let apple = Apple::new();
    let mut bag = Vec::new();
    bag.push(apple); // Give ownership
    bag.push(Apple::new());
*   deliver(bag); // Give ownership of bag and it's contents
}

/// deliver function takes ownership
/// of the vector
fn deliver(bag: Vec<Apple>) {
    // ...
}
```

---

# What if I want to use bag again?

```rust
fn main() {
    let apple = Apple::new();
    let mut bag = Vec::new();
    bag.push(apple);
    bag.push(Apple::new());
*   let (weight, bag) = weight(bag); // Return the bag back
    println!("Bag {}, weights {}", bag, weight);
}

/// weight function takes an owned bag
/// and return its weight and the bag back
fn weight(bag: Vec<Apple>) -> (u32, Vec<Apple>) {
    // ...
}
```

---

# Borrowing

<img src="content/images/rust-meetup-children-borrowing-0r.png" alt="Borrowing" width="300rem" height="auto" style="position: absolute; right: 3rem; margin-top: 0rem">

```rust
fn main() {
    let apple = Apple::new();
    let mut bag = Vec::new();
    bag.push(apple);
    bag.push(Apple::new());
*   let weight = weight(&bag); // Borrow the bag
    println!("Bag {}, weights {}", bag, weight);
}

/// weight function takes a shared
/// reference to the vector
fn weight(bag: &Vec<Apple>) -> u32 {
    // ...
}
```

---

# Mutable borrowing

<img src="content/images/rust-meetup-children-borrowing-0r.png" alt="Borrowing" width="300rem" height="auto" style="position: absolute; right: 3rem; margin-top: 0rem">

```rust
fn main() {
    let apple = Apple::new();
    let mut bag = Vec::new();
    bag.push(apple);
    bag.push(Apple::new());
*   deliver(&mut bag);        // Borrow the bag for mutation
    println!("Bag is now {}", bag);
}

/// deliver function takes a mutable shared
/// reference to the vector
fn deliver(bag: &mut Vec<Apple>) {
    // mutate the bag
}
```

---

# Dangers of mutation

```rust
let mut buffer = format!("Hello");
let slice = &buffer[1..];
buffer.push_str(" World");
println!("{:?}", slice);
```

---

# Dangers of mutation

```rust
*let mut buffer = format!("Hello");
let slice = &buffer[1..];
buffer.push_str(" World");
println!("{:?}", slice);
```

<img src="content/images/rust-meetup-mutation-1r.png" alt="Mutation 1">

---

# Dangers of mutation

```rust
let mut buffer = format!("Hello");
*let slice = &buffer[1..];
buffer.push_str(" World");
println!("{:?}", slice);
```

<img src="content/images/rust-meetup-mutation-2r.png" alt="Mutation 2">

---

# Dangers of mutation

```rust
let mut buffer = format!("Hello");
let slice = &buffer[1..];
*buffer.push_str(" World");
println!("{:?}", slice);
```

<img src="content/images/rust-meetup-mutation-3r.png" alt="Mutation 3">

---

# Dangers of mutation

```rust
let mut buffer = format!("Hello");
let slice = &buffer[1..];
*buffer.push_str(" World");
println!("{:?}", slice);
```

<img src="content/images/rust-meetup-mutation-4r.png" alt="Mutation 4">

---

# Dangers of mutation

```rust
let mut buffer = format!("Hello");
let slice = &buffer[1..];
*buffer.push_str(" World");
println!("{:?}", slice);
```

<img src="content/images/rust-meetup-mutation-5r.png" alt="Mutation 5">

---

# Dangers of mutation

```rust
let mut buffer = format!("Hello");
let slice = &buffer[1..];
*buffer.push_str(" World");
println!("{:?}", slice);
```

<img src="content/images/rust-meetup-mutation-6r.png" alt="Mutation 6">

???

- No aliasing + mutation at the same time

---

# Lifetime of a borrow

```rust
let mut buffer = format!("Hello");
*let slice = &buffer[1..];
*buffer.push_str(" World");
*println!("{:?}", slice);
```

**Lifetime**: span of code where reference is used.

--

**Rules**:

- If there's a shared reference, no writers during the **lifetime of the shared borrow**.
- If there's a mutable reference, no other readers or writers during the **lifetime of the mutable borrow**

---

# Sharing "freezes" data (temporarily)

```rust
let mut buffer = format!("Hello");
let slice = &buffer;       // buffer borrowed here
buffer.push_str(" World"); // cannot mutate while shared
slice.push_str(" World");  // cannot mutate through a shared ref
println!("{:?}", slice);   // reading slice ok while shared
buffer.push_str(" World"); // after last use of slice, buffer is mutable again
```

---

# Sharing "freezes" data (temporarily)

```rust
let mut buffer = format!("Hello");
*let slice = &buffer;       // buffer borrowed here
*buffer.push_str(" World"); // cannot mutate while shared
*slice.push_str(" World");  // cannot mutate through a shared ref
*println!("{:?}", slice);   // reading slice ok while shared
buffer.push_str(" World"); // after last use of slice, buffer is mutable again
```

---

# Mutable references: no other access to data

```rust
let mut buffer = format!("Hello");
buffer.push_str(" World");    // buffer mutated in place
let slice = &mut buffer;      // buffer mutably borrowed here
println!("{:?}", buffer);     // cannot access buffer while mutably borrowed
slice.push_str(" World");     // but can mutate through slice
buffer.push_str(" World");    // after last use of slice, buffer is accessible
```

---

# Mutable references: no other access to data

```rust
let mut buffer = format!("Hello");
buffer.push_str(" World");    // buffer mutated in place
*let slice = &mut buffer;      // buffer mutably borrowed here
*println!("{:?}", buffer);     // cannot access buffer while mutably borrowed
*slice.push_str(" World");     // but can mutate through slice
buffer.push_str(" World");    // after last use of slice, buffer is accessible
```

---

# Memory safety: a strict compiler

- It can take some time until your program compiles
- Lifetimes can be complicated
    - “error: `x` does not live long enough”


However:
- “If it compiles, it usually works”
- Far less debugging
    - No data races!
- Refactoring is safe and painless

---

<img src="content/images/rust-logo-blk.svg" alt="Rust logo" width="250rem" height="auto" style="position: absolute; right: 0rem; margin-top: -2rem;">

# Why use Rust?

- Abstraction without overhead
- Memory safety without garbage collection
- **Concurrency without data races**

---

# Data races

<table style="border-bottom-style: none; border-top-style: none;">
    <tbody>
        <tr>
            <td width="22%">&nbsp;&nbsp;&nbsp;Sharing</td>
	    <td>Actor-based languages</td>
        </tr>
        <tr>
            <td>+ Mutation</td>
	    <td>Functional languages</td>
        </tr>
        <tr>
            <td>+ No ordering</td>
	    <td>Sequential programming</td>
        </tr>
        <tr>
            <td style="border-bottom-style: solid"></td>
	    <td></td>
        </tr>
        <tr>
            <td>&nbsp;&nbsp;&nbsp;Data race</td>
	    <td></td>
        </tr>
    </tbody>
</table>

---

# Data races

<table style="border-bottom-style: none; border-top-style: none;">
    <tbody>
        <tr>
            <td width="22%" style="border-top-style: solid; border-left-style: solid; border-right-style: solid;">&nbsp;&nbsp;&nbsp;Sharing</td>
	    <td>Rust: no sharing and mutation</td>
	    <td></td>
        </tr>
        <tr>
            <td style="border-bottom-style: solid; border-left-style: solid; border-right-style: solid;">+ Mutation</td>
	    <td>at the same time</td>
	    <td></td>
        </tr>
        <tr>
            <td>+ No ordering</td>
	    <td></td>
	    <td></td>
        </tr>
        <tr>
            <td style="border-bottom-style: solid"></td>
	    <td></td>
	    <td></td>
        </tr>
        <tr>
            <td>&nbsp;&nbsp;&nbsp;Data race</td>
	    <td></td>
	    <td></td>
        </tr>
    </tbody>
</table>

---

# Parallelism

Observation:

- Building parallel abstractions is easy.
- Misusing those abstractions is also easy.

```go
func foo(channel chan<- map[string]string) {
	m := make(map[string]string)
	m["Hello"] = "World"
	channel <- m              // send data over channel
	m["Hello"] = "Data Race"  // but how to stop sender from using
	                          // it afterwards?
}
```

---

# Parallelism

```rust
fn foo(channel: &mut Channel) {
  let m = HashMap::new();
  m.insert("Hello", "World");
  channel.send(m);
  m.insert("Hello", "Data Race"); // **Error**: use of moved value: \`m`
}

impl<T> Channel<T> {
  // Takes ownership of the data
  fn send(&mut self, data: T) {
    // ...
  }
}
```

---

# Parallelism

```rust
fn load_images(paths: &[PathBuf]) -> Vec<Image> {
    paths.iter() // For each path ...
         .map(|path| {
             Image::load(path) // ... load an image ...
         })
	 .collect() // ... create and return a vector.
}
```

--

```rust
extern crate rayon; // Third-party library

fn load_images(paths: &[PathBuf]) -> Vec<Image> {
    paths.par_iter() // Make it parallel
         .map(|path| {
             Image::load(path)
         })
	 .collect()
}
```

---

# Parallelism with mutability

```rust
fn load_images(paths: &[PathBuf]) -> Vec<Image> {
    let mut jpgs = 0; // How many jpgs seen so far?
    paths.par_iter() // Make it parallel
         .map(|path| {
            // If current file name ends in "jpg" ...
             if path.ends_with(".jpg") {
                 // multiple mutable borrows occurr here
                 jpgs += 1;
             }
             Image::load(path)
         })
         .collect()
}
```

---

# Parallelism with mutability

```rust
fn load_images(paths: &[PathBuf]) -> Vec<Image> {
    let mut jpgs = 0; // How many jpgs seen so far?
    paths.par_iter() // Make it parallel
         .map(|path| {
            // If current file name ends in "jpg" ...
             if path.ends_with(".jpg") {
                 // multiple mutable borrows occurr here
*                jpgs += 1;
             }
             Image::load(path)
         })
         .collect()
}
```

Rust prevents a data race at compile time.

---

# A powerful type system: mutexes

<table>
    <thead><tr><th width="50%" style="text-align: center">C++</th><th width="50%">Rust</th></tr></thead>
    <tbody>
        <tr>
            <td>
<pre><code class="C++">std::vector<int> data = {1, 2, 3};
// mutex is unrelated to data
std::mutex mutex;

// unsynchronized access possible
data.push_back(4);

mutex.lock();
data.push_back(5);
mutex.unlock();
</pre></code>
            </td><td>
<pre><code class="Rust">let data = vec![1, 2, 3];
// mutex owns data
let mutex = Mutex::new(data);

// compilation error: data was moved
data.push(4);

let mut d = mutex.lock().unwrap();
d.push(5);
// released at end of scope
</pre></code>
            </td>
        </tr>
    </tbody>
</table>

⇒ Rust ensures that Mutex is locked before accessing data

???

- Impossible to access data behind a Mutex without locking
- Represent contracts in code instead of documentation

---

<img src="content/images/cargo-logo.png" alt="Cargo logo" width="150rem" height="auto" style="position: absolute; right: 0rem; margin-top: -2rem;">

# Great tooling

- **rustup**: Use multiple Rust versions for different directories
- **cargo**: Automatically download, build, and link dependencies
- **rustfmt**: Format Rust code according to style guidelines

--
- **Rust Playground**: Run and share code snippets in your browser

![Rust Playground screenshot](content/images/playground.png)

---

# Great tooling

- **clippy**: Additional warnings for dangerous or unidiomatic code

```rust
fn equal(x: f32, y: f32) -> bool {
    if x == y { true } else { false }
}
```
--

```
error: `strict comparison of f32 or f64`
 --> src/main.rs:2:8
  |
2 | if x == y { true } else { false }
  |    ^^^^^^ help: `consider comparing them within some error: (x - y).abs() < err`

warning: `this if-then-else expression returns a bool literal`
 --> src/main.rs:2:5
  |
2 | if x == y { true } else { false }
  | ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ help: `you can reduce it to: x == y`
```

---

# To sum up

- Rust is a high perfomant and safe systems programming language
--

- Used by a lot of big names already (Mozilla, Google, Microsoft, Facebook, Twitter, etc)
--

- You can use it for:
--

  - Systems programming in general
--

  - Fix hot stops of your high level app
--

  - WebAssembly
--

  - Web APIs
--

  - Networking solutions and/or blockchain solutions
--

  - Embedded or IoT systems
--

  - Games
--

  - Machine learning
--

  - Cli
--

  - Etc

---

class: center
name: title
count: false

# Rust Latam

<img src="content/images/rustlatam.png" alt="Rust Latam" width="600rem" height="auto">

https://rustlatam.org

@rustlatamconf

---

class: center
name: title
count: false

<img src="content/images/rust-logo-blk.svg" alt="Rust logo" width="250rem" height="auto">

# Thanks

.grey[Twitter/Github: spastorino]<br/>
.grey[Email: spastorino@gmail.com]
