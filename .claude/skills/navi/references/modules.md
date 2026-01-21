# Module System

Complete guide to Navi's module system, imports, and visibility control.

## Module Structure

### Directory-Based Modules

```
project/
├── main.nv              # Main module (root)
├── utils.nv             # Part of main module
├── config/              # config module
│   ├── dev.nv          # Part of config module
│   └── prod.nv         # Part of config module
├── models/              # models module
│   ├── user.nv         # Part of models module
│   └── post/           # models.post sub-module
│       ├── comment.nv  # Part of models.post
│       └── like.nv     # Part of models.post
└── services/            # services module
    └── api.nv          # Part of services module
```

**Key Rules:**
- Root directory is the `main` module
- Each subdirectory is a module (named after directory)
- All `.nv` files in same directory are part of same module
- Modules must be linked with `use` to be compiled

### Entry Points

```nv
// main.nv is default entry point
fn main() throws {
    // ...
}

// Or specify file explicitly
// navi run custom.nv
```

## Importing Modules

### Standard Library

```nv
// Import module
use std.io;

// Import type/struct
use std.url.Url;

// Import with alias
use std.http as client;

// Import multiple
use std.{io, json, time};

// Import constant/variable
use std.net.http.NotFound;
```

### Local Modules

```nv
// In main.nv
use config;           // Import config module
use models;           // Import models module
use models.post;      // Import sub-module
use services;         // Import services module

fn main() throws {
    let cfg = config.load();
    let user = models.User.new("Alice");
    let post = models.post.Post.new();
}
```

### Module Access

```nv
// After importing
use std.io;

// Last part becomes name
io.println("Hello");  // io is the name

use std.url.Url;
let url = Url.parse("https://example.com");  // Url is the name
```

## Visibility Control

### Public Items

```nv
// Public struct (accessible from other modules)
pub struct User {
    pub name: string,      // Public field
    email: string?,        // Private field
    id: int,               // Private field
}

// Public function
pub fn create_user(name: string): User {
    return User {
        name,
        email: nil,
        id: generate_id(),
    };
}

// Public constant
pub const MAX_USERS: int = 1000;

// Public variable
pub let global_config = load_config();
```

### Private Items

```nv
// Private (module-only) by default
struct InternalData {
    value: int,
}

fn helper_function(): string {
    return "helper";
}

const INTERNAL_CONSTANT = 42;
```

### Visibility Rules

```nv
// In models/user.nv
pub struct User {
    pub name: string,     // Accessible everywhere
    email: string?,       // Accessible only in models module
}

pub fn public_api() {
    // Accessible everywhere
}

fn internal_helper() {
    // Accessible only in models module
}

// In models/post.nv (same module)
use models;

fn example() {
    let user = models.User { name: "Alice", email: nil };  // OK - same module
    models.internal_helper();  // OK - same module
}

// In services/api.nv (different module)
use models;

fn example() {
    let user = models.User { name: "Alice", email: nil };  // ERROR - email is private
    models.internal_helper();  // ERROR - function is private

    let user = models.create_user("Alice");  // OK - public function
    println(user.name);  // OK - public field
}
```

## Module Linking

**Important:** Modules must be linked to be compiled!

```nv
// main.nv
use config;    // Links config module
use models;    // Links models module

// Now config/ and models/ will be compiled
// Without these use statements, navi test won't find them
```

## Module Patterns

### Pattern 1: Module Initialization

```nv
// config/mod.nv (or any file in config/)
pub struct Config {
    host: string,
    port: int,
}

pub fn load(): Config {
    return Config {
        host: "localhost",
        port: 8080,
    };
}

pub const DEFAULT_TIMEOUT: float = 30.0;
```

### Pattern 2: Re-exports

```nv
// models/mod.nv
pub use models.user.User;
pub use models.post.Post;

// Now users can:
use models;
let user = models.User.new();  // Instead of models.user.User
```

### Pattern 3: Module Facade

```nv
// api/mod.nv
use api.client;
use api.request;
use api.response;

// Expose simplified interface
pub fn get(url: string): Response throws {
    let req = request.Request.new(url);
    return try client.send(req);
}

pub fn post(url: string, data: string): Response throws {
    let req = request.Request.new(url).with_body(data);
    return try client.send(req);
}
```

### Pattern 4: Shared Types

```nv
// types/mod.nv
pub type UserId = int;
pub type Timestamp = float;

pub struct Result<T> {
    pub value: T?,
    pub error: string?,
}

// Use across modules
use types;

fn get_user(id: types.UserId): types.Result<User> {
    // ...
}
```

## Best Practices

### Module Organization

```nv
// Good: Logical grouping
models/
  user.nv
  post.nv
  comment.nv

services/
  auth.nv
  database.nv
  cache.nv

// Bad: Everything in one directory
user.nv
post.nv
auth.nv
database.nv
```

### Visibility Guidelines

```nv
// Default to private
struct InternalCache {
    data: <string, Any>,
}

// Make public only what's needed
pub struct PublicApi {
    // Public interface
}

impl PublicApi {
    pub fn public_method(self) {
        self.internal_helper();  // Can call private
    }

    fn internal_helper(self) {
        // Private implementation
    }
}
```

### Import Organization

```nv
// Group imports logically
// 1. Standard library
use std.io;
use std.json;
use std.time;

// 2. Local modules
use models;
use services;
use config;

// 3. Keep in alphabetical order within groups
```

### Avoid Circular Dependencies

```nv
// Bad: Circular dependency
// models/user.nv
use models.post;  // User depends on Post

// models/post.nv
use models.user;  // Post depends on User - CIRCULAR!

// Good: Extract shared types
// types/common.nv
pub type UserId = int;
pub type PostId = int;

// models/user.nv
use types.common;

pub struct User {
    id: common.UserId,
}

// models/post.nv
use types.common;

pub struct Post {
    id: common.PostId,
    author_id: common.UserId,
}
```

## Module Testing

```nv
// Each module can have tests
// models/user.nv
pub struct User {
    pub name: string,
}

test "user creation" {
    let user = User { name: "Alice" };
    assert_eq user.name, "Alice";
}

// Run tests
// navi test              # All modules
// navi test models/      # Specific module
// navi test models/user.nv  # Specific file
```

## Standard Library Modules

Common standard library modules:

```nv
use std.io;           // Input/output
use std.json;         // JSON parsing
use std.time;         // Time operations
use std.url;          // URL parsing
use std.net.http;     // HTTP client
use std.backtrace;    // Stack traces
use std.range;        // Range utilities
```

## Module Loading

```nv
// Navi loads modules when they're used
use models;  // Loads models module

// Transitive imports
// If models imports users, you don't need to import users
// unless you use it directly

// Circular imports are not allowed
```

## Naming Conventions

```nv
// Module names: snake_case directory names
user_profile/
api_client/

// File names: snake_case
user_model.nv
api_client.nv

// But typically match module
models/
  user.nv  // Contains User struct
  post.nv  // Contains Post struct
```

## Common Module Patterns

### Singleton Pattern

```nv
// config/global.nv
struct GlobalConfig {
    initialized: bool = false,
}

let global_instance: GlobalConfig = GlobalConfig {};

pub fn instance(): GlobalConfig {
    if (!global_instance.initialized) {
        // Initialize once
        global_instance.initialized = true;
    }
    return global_instance;
}
```

### Factory Pattern

```nv
// models/user.nv
pub struct User {
    name: string,
    email: string?,
}

impl User {
    pub fn new(name: string): User {
        return User {
            name,
            email: nil,
        };
    }

    pub fn with_email(name: string, email: string): User {
        return User {
            name,
            email,
        };
    }
}
```

### Registry Pattern

```nv
// services/registry.nv
pub struct ServiceRegistry {
    services: <string, Service>,
}

impl ServiceRegistry {
    pub fn new(): ServiceRegistry {
        return ServiceRegistry { services: {:} };
    }

    pub fn register(self, name: string, service: Service) {
        self.services[name] = service;
    }

    pub fn get(self, name: string): Service? {
        return self.services[name];
    }
}
```
