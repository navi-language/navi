# Navi Type System

Comprehensive guide to Navi's type system including structs, enums, interfaces, optional types, and type aliases.

## Structs

### Definition and Creation

```nv
struct User {
    name: string,
    email: string?,
    age: int,
    active: bool = true,  // Default value
}

// Create instance
let user = User {
    name: "Alice",
    email: nil,
    age: 30,
    // active uses default
};

// Short syntax
let name = "Bob";
let age = 25;
let user2 = User {
    name,  // Same as name: name
    age,   // Same as age: age
    email: nil,
};
```

### Methods

```nv
impl User {
    // Static method (no self parameter)
    fn new(name: string, age: int): User {
        return User {
            name,
            age,
            email: nil,
        };
    }

    // Instance method (has self parameter)
    fn greet(self): string {
        return `Hello, I'm ${self.name}`;
    }

    // Method that mutates self
    fn set_email(self, email: string) {
        self.email = email;
    }

    // Public method (accessible from other modules)
    pub fn get_name(self): string {
        return self.name;
    }
}

// Usage
let user = User.new("Alice", 30);  // Static
println(user.greet());              // Instance
```

### Interface Implementation

```nv
interface ToString {
    fn to_string(self): string;
}

// Implicit implementation (just add the method)
impl User {
    fn to_string(self): string {
        return `User(${self.name}, ${self.age})`;
    }
}

// Explicit implementation (for clarity)
impl ToString for User {
    fn to_string(self): string {
        return `User(${self.name}, ${self.age})`;
    }
}
```

### Serialization Attributes

```nv
// Rename all fields
#[serde(rename_all = "camelCase")]
struct ApiUser {
    user_name: string,  // Becomes "userName"
    user_id: int,       // Becomes "userId"
}

// Field-level control
struct User {
    #[serde(rename = "fullName")]
    name: string,

    #[serde(alias = "mail")]  // Accept both "email" and "mail"
    email: string,

    #[serde(skip)]  // Don't serialize/deserialize
    password: string = "",

    #[serde(flatten)]  // Flatten nested structure
    metadata: <string, Any>,
}

// Deny unknown fields during deserialization
#[serde(deny_unknown_fields)]
struct StrictConfig {
    host: string,
    port: int,
}
```

## Enums

### Basic Enums

```nv
// Simple enum (values start at 0)
enum Status {
    Active,    // 0
    Inactive,  // 1
    Pending,   // 2
}

// Explicit values
enum HttpCode {
    Ok = 200,
    NotFound = 404,
    ServerError = 500,
}

// Convert to int
let code = HttpCode.Ok as int;  // 200
```

### Enum Literals

```nv
// Type can be omitted when inferable
let status: Status = .Active;

fn check_status(s: Status) {
    // ...
}

check_status(.Active);  // Same as Status.Active
```

### Enum Attributes

```nv
// Serialize as integer
#[serde(int)]
enum Priority {
    Low,     // 0
    Medium,  // 1
    High,    // 2
}

// Rename variants
#[serde(rename_all = "UPPERCASE")]
enum Role {
    Admin,  // "ADMIN"
    User,   // "USER"
}

// Field-level rename
enum Status {
    #[serde(rename = "active")]
    Active,

    #[serde(alias = "disabled")]  // Accept both
    Inactive,
}
```

## Interfaces

### Definition

```nv
interface Read {
    // Required method
    fn read(self): string throws;

    // Method with default implementation
    fn read_all(self): string throws {
        return self.read();
    }
}

interface Write {
    fn write(self, data: string) throws;
}

// Interface inheritance
interface ReadWrite: Read, Write {
    fn flush(self);
}
```

### Implementation

```nv
struct File {
    path: string,
}

impl Read for File {
    fn read(self): string throws {
        // Implementation
        return "data";
    }
    // read_all uses default implementation
}

impl Write for File {
    fn write(self, data: string) throws {
        // Implementation
    }
}

// Now File implements Read, Write, and can be used as ReadWrite
```

### Using Interfaces

```nv
fn process(reader: Read): string throws {
    return try reader.read_all();
}

let file = File { path: "test.txt" };
let content = try process(file);
```

### Interface Conversion

```nv
interface Base {
    fn base_method(self): string;
}

interface Derived: Base {
    fn derived_method(self): string;
}

struct MyType {}

impl MyType {
    fn base_method(self): string {
        return "base";
    }
    fn derived_method(self): string {
        return "derived";
    }
}

let obj = MyType {};
let derived: Derived = obj;
let base: Base = derived;  // Implicit upcast to parent interface
```

## Optional Types

### Declaration and Use

```nv
// Optional type with ?
let name: string? = "Alice";
let empty: string? = nil;

// Check if nil
if (name == nil) {
    println("No name");
}
if (name != nil) {
    println("Has name");
}
```

### Unwrapping

```nv
let value: int? = 42;

// Force unwrap (panics if nil)
let n = value!;

// Unwrap or default
let n = value || 0;

// Optional chaining
struct User {
    profile: Profile?,
}

struct Profile {
    bio: string?,
}

let user: User? = get_user();
let bio = user?.profile?.bio;  // Returns string?? collapsed to string?
```

### If-Let

```nv
if (let v = optional_value) {
    // v is unwrapped here
    println(v);
} else {
    println("Was nil");
}
```

### Let-Else

```nv
let value = optional_value else {
    // Handle nil case
    return default;
};
// value is unwrapped here
```

### Optional Methods

```nv
let opt: string? = "hello";

// Check
opt.is_nil()                    // false

// Transform
opt.map(|s| s.len())            // Some(5)
opt.map_or(0, |s| s.len())      // 5 (or 0 if nil)

// Chain
opt.and("world")                // Some("world") if opt is Some
opt.and_then(|s| `${s}!`)       // Transform and flatten

opt.or("default")               // opt if Some, else "default"
opt.or_else(|| get_default())   // Lazy default

// Unwrap
opt.unwrap()                    // Panics if nil
opt.expect("Required value")    // Panic with custom message
opt.unwrap_or("default")        // Provide default
opt.unwrap_or_else(|| "default") // Lazy default
```

## Union Types

### Definition and Use

```nv
// Union of multiple types
fn process(value: int | string | float): string {
    switch (let v = value.(type)) {
        case int:
            return `int: ${v}`;
        case float:
            return `float: ${v}`;
        case string:
            return `string: ${v}`;
    }
}

// In struct fields
struct Data {
    value: int | string,
}

// In return types
fn get_value(): (int | string) {
    return 42;
}
```

### Type Assertions

```nv
let value: Any = "hello";

// Assert to specific type
let s = value.(string);  // Returns string, panics if wrong type

// Optional type assertion
if (let s = value.(string)) {
    // s is string here
}

// With optional values
let opt_value: Any? = 10;
let num = opt_value?.(int);  // Returns int? (nil if opt_value is nil)
```

## Type Aliases

### Type Alias (Same Type)

```nv
// Alias is same as original type
type alias UserId = int;
type alias Config = <string, string>;

let id: UserId = 123;
id.to_string();  // Has all int methods

// Can be used interchangeably
let num: int = id;
let id2: UserId = num;
```

### New Type (Different Type)

```nv
// New type wraps original but is distinct
type MyString = string;

impl MyString {
    pub fn len(self): int {
        return (self as string).len();
    }
}

let s = "hello" as MyString;
s.len();  // Only has custom methods
// s.to_uppercase();  // ERROR: MyString doesn't have this

// Convert back explicitly
let original = s as string;
original.to_uppercase();  // OK
```

## Generic Type Notation

While Navi doesn't have full generics yet, it uses generic notation in some contexts:

```nv
// Channel with type parameter
let ch = channel::<int>();

// Parse with type parameter
let user = json.parse::<User>(data);

// Collection types
let items: [int] = [];
let map: <string, int> = {:};
```

## Type Inference

```nv
// Type inferred from value
let num = 42;           // int
let text = "hello";     // string
let flag = true;        // bool

// Type inferred from usage
let items = [];         // Type determined by first use
items.push(1);          // Now [int]

// Inferred from function return
fn get_number(): int {
    return 42;
}
let n = get_number();   // int inferred
```

## Any Type

```nv
// Any can hold any value
let value: Any = 42;
let value: Any = "hello";
let value: Any = User { name: "Alice", age: 30 };

// Use type switch to handle
switch (let v = value.(type)) {
    case int:
        println(`int: ${v}`);
    case string:
        println(`string: ${v}`);
    case User:
        println(`user: ${v.name}`);
    default:
        println("unknown");
}
```

## Type Best Practices

### When to Use Optional vs Result

```nv
// Use optional for values that may not exist
fn find_user(id: int): User? {
    // ...
}

// Use throws for operations that can fail
fn load_config(path: string): Config throws {
    // ...
}
```

### Struct vs Type Alias

```nv
// Use struct for domain concepts with behavior
struct UserId {
    value: int,
}

impl UserId {
    fn validate(self): bool {
        return self.value > 0;
    }
}

// Use type alias for simple names
type alias Duration = float;  // Seconds
type alias Callback = |(): void|;
```

### Interface Design

```nv
// Keep interfaces focused (single responsibility)
interface Serializable {
    fn serialize(self): string;
}

interface Deserializable {
    fn deserialize(data: string): Self throws;
}

// Compose interfaces
interface Storable: Serializable, Deserializable {
    fn save(self) throws;
}
```

### Optional Chaining

```nv
// Good: Safe chaining with default
let email = user?.profile?.contact?.email || "no-email@example.com";

// Avoid: Deep nesting makes debugging hard
let value = a?.b?.c?.d?.e?.f?.g;

// Better: Break into steps with meaningful names
let profile = user?.profile else { return "no profile"; };
let contact = profile.contact else { return "no contact"; };
let email = contact.email || "no email";
```

### Type Safety with New Types

```nv
// Bad: Easy to mix up
fn transfer(from: int, to: int, amount: int) {
    // Which int is which?
}

// Good: Type safety with new types
type AccountId = int;
type Amount = int;

fn transfer(from: AccountId, to: AccountId, amount: Amount) {
    // Clear distinction
}
```
