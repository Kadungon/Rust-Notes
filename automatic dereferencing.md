Rust's **automatic dereferencing**, also known as **deref coercion**, is a convenience feature that applies in specific contexts. It simplifies accessing values by removing the need to explicitly dereference pointers or references. However, deref coercion does not apply universally, and understanding its rules will help clarify when it works automatically and when explicit dereferencing (`*`) is needed.

---

### When Rust Performs Deref Coercion Automatically

Rust will apply deref coercion automatically in these contexts:

#### 1. **Method Calls**
When calling a method, Rust will automatically dereference as many times as needed to match the method's receiver type (e.g., `self`, `&self`, `&mut self`).

```rust
fn example(v: &&i32) {
    println!("{}", v.to_string()); // Automatically dereferences `&&i32` to `i32`
}
```

Here, the `to_string()` method is implemented for `i32`, not `&&i32`. Rust dereferences `v` twice to get the `i32`.

---

#### 2. **Trait Implementations**
When passing a value to a function or operator that requires a specific type, Rust applies deref coercion to match the expected type.

```rust
fn add_one(x: i32) -> i32 {
    x + 1
}

let v = &5;
add_one(*v);      // Explicit dereference
add_one(v);       // Automatic dereference
```

Rust dereferences `v` automatically because `add_one` expects an `i32`.

---

#### 3. **Operators**
For operators like `+`, `-`, `*`, Rust will dereference references to match the type required by the operator's implementation.

```rust
let v = &10;
let result = v + 20; // Rust dereferences `v` automatically to perform addition
```

---

#### 4. **Pattern Matching**
When matching references in patterns, Rust dereferences them to make working with the underlying data easier.

```rust
let v = &Some(5);
if let Some(n) = v {
    println!("{n}"); // Automatically dereferences `v` to access `Some(5)`
}
```

---

### When Rust Does NOT Perform Deref Coercion

There are scenarios where deref coercion does **not** apply, requiring explicit dereferencing:

#### 1. **When Accessing a Value Directly**
If you're accessing a value directly (not through a method or operator), Rust does not assume automatic dereferencing.

```rust
let v = &&10;
let x = **v; // Explicit dereference required
```

Here, `**v` is needed because you're directly accessing the value without any context that requires deref coercion.

---

#### 2. **Nested References**
Rust will not automatically "peel off" multiple layers of references (`&&T`, `&&&T`, etc.) unless the context explicitly requires it.

```rust
let v = &&5;
let x = **v; // Required because there's no implicit deref context
```

---

#### 3. **Assignment or Explicit Usage**
If you’re performing assignments or trying to manipulate references explicitly, Rust won’t dereference for you.

```rust
let v = &mut 5;
*v += 1; // Explicit dereferencing is required
```

---

#### 4. **Ambiguous Context**
When dereferencing could lead to ambiguous or unintended behavior, Rust requires explicit dereferencing to maintain safety and clarity.

```rust
fn ambiguous(v: &i32, x: i32) -> i32 {
    v + x // This works because `+` triggers deref coercion
}

let v = &&10;
// ambiguous(v, 5); // Error: Rust does not dereference `&&i32` to `i32` here
let result = ambiguous(*v, 5); // Explicit dereference required
```

---

### Summary Table

| **Context**                | **Automatic Deref Applies?** | **Reason**                                                                 |
|----------------------------|-----------------------------|----------------------------------------------------------------------------|
| Method calls               | ✅                         | Rust matches the method's expected receiver type.                         |
| Operators (`+`, `-`, etc.) | ✅                         | Rust dereferences to match the operator's expected operand type.          |
| Function arguments         | ✅                         | Rust dereferences to match the expected type of the function parameter.   |
| Direct access              | ❌                         | Rust does not dereference unless explicitly instructed.                   |
| Nested references          | ❌                         | Rust does not automatically peel multiple layers of references.           |
| Assignments                | ❌                         | Explicit dereferencing is needed to modify the underlying value.          |

---

### Mental Model

Think of deref coercion as a convenience that kicks in **only when the context demands it** (e.g., matching a method signature, operator, or expected function type). For direct or ambiguous operations, Rust errs on the side of explicitness to ensure clarity and safety.
