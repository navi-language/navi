# Common Patterns and Idioms

Collection of idiomatic Navi code patterns and anti-patterns.

## Structural Patterns

### Builder Pattern

```nv
struct Config {
    host: string = "localhost",
    port: int = 8080,
    timeout: float = 30.0,
    debug: bool = false,
}

impl Config {
    pub fn new(): Config {
        return Config {};
    }

    pub fn with_host(self, host: string): Config {
        self.host = host;
        return self;
    }

    pub fn with_port(self, port: int): Config {
        self.port = port;
        return self;
    }

    pub fn with_timeout(self, timeout: float): Config {
        self.timeout = timeout;
        return self;
    }

    pub fn debug(self): Config {
        self.debug = true;
        return self;
    }
}

// Usage
let config = Config.new()
    .with_host("example.com")
    .with_port(3000)
    .debug();
```

### Factory Pattern

```nv
enum DatabaseType {
    Postgres,
    MySQL,
    SQLite,
}

interface Database {
    fn connect(self) throws;
    fn query(self, sql: string): Result throws;
}

struct DatabaseFactory {}

impl DatabaseFactory {
    pub fn create(db_type: DatabaseType, config: Config): Database {
        switch (db_type) {
            case .Postgres:
                return PostgresDb.new(config);
            case .MySQL:
                return MySQLDb.new(config);
            case .SQLite:
                return SQLiteDb.new(config);
        }
    }
}

// Usage
let db = DatabaseFactory.create(.Postgres, config);
try db.connect();
```

### Singleton Pattern

```nv
struct Logger {
    level: LogLevel,
    initialized: bool = false,
}

let global_logger: Logger = Logger { level: .Info };

impl Logger {
    pub fn instance(): Logger {
        if (!global_logger.initialized) {
            global_logger.initialized = true;
            // Initialize
        }
        return global_logger;
    }

    pub fn log(self, message: string) {
        println(`[${self.level}] ${message}`);
    }
}

// Usage
Logger.instance().log("Application started");
```

### Strategy Pattern

```nv
interface SortStrategy {
    fn sort(self, data: [int]): [int];
}

struct BubbleSort {}
impl SortStrategy for BubbleSort {
    fn sort(self, data: [int]): [int] {
        // Bubble sort implementation
        return data;
    }
}

struct QuickSort {}
impl SortStrategy for QuickSort {
    fn sort(self, data: [int]): [int] {
        // Quick sort implementation
        return data;
    }
}

struct Sorter {
    strategy: SortStrategy,
}

impl Sorter {
    pub fn new(strategy: SortStrategy): Sorter {
        return Sorter { strategy };
    }

    pub fn sort(self, data: [int]): [int] {
        return self.strategy.sort(data);
    }
}

// Usage
let sorter = Sorter.new(QuickSort {});
let sorted = sorter.sort([3, 1, 4, 1, 5]);
```

## Error Handling Patterns

### Result Type Pattern

```nv
struct Result<T> {
    value: T?,
    error: string?,
}

impl Result {
    pub fn ok(value: T): Result<T> {
        return Result { value, error: nil };
    }

    pub fn err(message: string): Result<T> {
        return Result { value: nil, error: message };
    }

    pub fn is_ok(self): bool {
        return self.error == nil;
    }

    pub fn is_err(self): bool {
        return self.error != nil;
    }

    pub fn unwrap(self): T {
        if (let v = self.value) {
            return v;
        }
        panic `Unwrapped error result: ${self.error!}`;
    }

    pub fn unwrap_or(self, default: T): T {
        return self.value || default;
    }

    pub fn map(self, f: |(T): U|): Result<U> {
        if (let v = self.value) {
            return Result.ok(f(v));
        }
        return Result.err(self.error!);
    }
}
```

### Early Return Pattern

```nv
fn process_request(req: Request): Response {
    // Validate
    let user = req.user else {
        return Response.error("No user");
    };

    // Check permissions
    if (!user.has_permission("read")) {
        return Response.error("No permission");
    }

    // Process
    let data = try? fetch_data(req.id) else {
        return Response.error("Data not found");
    };

    return Response.ok(data);
}
```

### Error Context Pattern

```nv
struct ContextError {
    context: string,
    inner: Error,
}

impl Error for ContextError {
    pub fn error(self): string {
        return `${self.context}: ${self.inner.error()}`;
    }
}

fn with_context(operation: |(): T throws|, context: string): T throws ContextError {
    do {
        return try operation();
    } catch (e) {
        throw ContextError { context, inner: e };
    }
}

// Usage
let result = try with_context(|| {
    return try load_file("config.json");
}, "Loading configuration");
```

## Optional Handling Patterns

### Safe Navigation

```nv
struct User {
    profile: Profile?,
}

struct Profile {
    settings: Settings?,
}

struct Settings {
    theme: string,
}

// Good: Safe chaining with default
let theme = user?.profile?.settings?.theme || "default";

// Good: Early return with let-else
let profile = user.profile else {
    return "default";
};

let settings = profile.settings else {
    return "default";
};

return settings.theme;
```

### Optional Transform Pattern

```nv
let user_name: string? = get_user_name();

// Transform if present
let upper = user_name.map(|n| n.to_uppercase());

// Transform with default
let display = user_name.map_or("Guest", |n| `Welcome ${n}`);

// Chain operations
let result = user_name
    .map(|n| n.trim())
    .map(|n| n.to_uppercase())
    .unwrap_or("UNKNOWN");
```

## Concurrency Patterns

### Worker Pool Pattern

```nv
fn worker_pool(jobs: [Job], worker_count: int) throws {
    let job_ch = channel::<Job>();
    let result_ch = channel::<Result>();

    // Spawn workers
    for (let i in 0..worker_count) {
        spawn {
            while (true) {
                let job = try? job_ch.recv() else {
                    break;
                };

                let result = process_job(job);
                try! result_ch.send(result);
            }
        }
    }

    // Send jobs
    spawn {
        for (let job in jobs) {
            try! job_ch.send(job);
        }
    }

    // Collect results
    let results: [Result] = [];
    for (let i in 0..jobs.len()) {
        results.push(try result_ch.recv());
    }

    return results;
}
```

### Pipeline Pattern

```nv
fn pipeline(input: [int]) throws {
    let stage1 = channel::<int>();
    let stage2 = channel::<int>();
    let stage3 = channel::<string>();

    // Stage 1: Multiply by 2
    spawn {
        for (let n in input) {
            try! stage1.send(n * 2);
        }
    }

    // Stage 2: Add 10
    spawn {
        for (let i in 0..input.len()) {
            let n = try! stage1.recv();
            try! stage2.send(n + 10);
        }
    }

    // Stage 3: Convert to string
    spawn {
        for (let i in 0..input.len()) {
            let n = try! stage2.recv();
            try! stage3.send(n.to_string());
        }
    }

    // Collect
    let results: [string] = [];
    for (let i in 0..input.len()) {
        results.push(try stage3.recv());
    }

    return results;
}
```

### Timeout Pattern

```nv
use std.time;

fn with_timeout(operation: |(): T|, timeout: Duration): T? {
    let result = channel::<T?>();

    spawn {
        let value = operation();
        try! result.send(value);
    }

    spawn {
        time.sleep(timeout);
        try! result.send(nil);
    }

    return try! result.recv();
}

// Usage
let result = with_timeout(|| {
    return expensive_operation();
}, 5.seconds());

if (let r = result) {
    println(`Success: ${r}`);
} else {
    println("Timeout");
}
```

## Resource Management Patterns

### RAII with Defer

```nv
struct File {
    path: string,
    handle: FileHandle,
}

impl File {
    pub fn open(path: string): File throws {
        let handle = try open_file_handle(path);
        return File { path, handle };
    }

    pub fn close(self) {
        close_file_handle(self.handle);
    }
}

fn process_file(path: string) throws {
    let file = try File.open(path);

    defer {
        file.close();
    }

    // Use file
    try file.read();
    // defer executes here
}
```

### Scoped Resource Pattern

```nv
fn with_lock(lock: Lock, operation: |(): T|): T {
    lock.acquire();

    defer {
        lock.release();
    }

    return operation();
}

// Usage
let result = with_lock(my_lock, || {
    return critical_section();
});
```

## Collection Patterns

### Filter-Map-Reduce Pattern

```nv
fn process_numbers(numbers: [int]): int {
    let filtered: [int] = [];

    // Filter
    for (let n in numbers) {
        if (n > 0) {
            filtered.push(n);
        }
    }

    // Map
    let mapped: [int] = [];
    for (let n in filtered) {
        mapped.push(n * 2);
    }

    // Reduce
    let sum = 0;
    for (let n in mapped) {
        sum += n;
    }

    return sum;
}
```

### Grouping Pattern

```nv
fn group_by_age(users: [User]): <int, [User]> {
    let groups: <int, [User]> = {:};

    for (let user in users) {
        if (groups[user.age] == nil) {
            groups[user.age] = [];
        }

        groups[user.age].push(user);
    }

    return groups;
}
```

## Type Patterns

### Type State Pattern

```nv
// Encode states in types
type Draft = Document;
type Published = Document;

struct Document {
    content: string,
    is_published: bool = false,
}

impl Document {
    pub fn new(): Draft {
        return Document { content: "", is_published: false } as Draft;
    }
}

impl Draft {
    pub fn edit(self, content: string): Draft {
        self.content = content;
        return self;
    }

    pub fn publish(self): Published {
        self.is_published = true;
        return self as Published;
    }
}

impl Published {
    pub fn get_content(self): string {
        return self.content;
    }

    // Can't edit published documents
}

// Usage
let doc = Document.new()
    .edit("Hello")
    .edit("World")
    .publish();

let content = doc.get_content();
// doc.edit("More");  // ERROR: Published doesn't have edit
```

### Newtype Pattern

```nv
// Strong typing with zero cost
type UserId = int;
type OrderId = int;

fn get_user(id: UserId): User {
    // ...
}

fn get_order(id: OrderId): Order {
    // ...
}

let user_id = 123 as UserId;
let order_id = 456 as OrderId;

get_user(user_id);     // OK
// get_user(order_id);  // ERROR: type mismatch
```

## Anti-Patterns

### Anti-Pattern 1: Overusing Force Unwrap

```nv
// Bad
let value = optional!;
let result = try! operation();

// Good
let value = optional || default;
let result = try? operation();
if (let r = result) {
    // Handle success
}
```

### Anti-Pattern 2: Deep Nesting

```nv
// Bad
if (condition1) {
    if (condition2) {
        if (condition3) {
            // ...
        }
    }
}

// Good: Early returns
if (!condition1) {
    return;
}
if (!condition2) {
    return;
}
if (!condition3) {
    return;
}
// ...
```

### Anti-Pattern 3: God Objects

```nv
// Bad
struct Application {
    database: Database,
    cache: Cache,
    logger: Logger,
    config: Config,
    // ... 20 more fields
}

// Good: Separate concerns
struct Application {
    services: Services,
    config: Config,
}

struct Services {
    database: Database,
    cache: Cache,
    logger: Logger,
}
```

### Anti-Pattern 4: Stringly-Typed

```nv
// Bad
fn get_user_role(role: string): Permissions {
    if (role == "admin") {
        // ...
    } else if (role == "user") {
        // ...
    }
}

// Good: Use enums
enum Role {
    Admin,
    User,
    Guest,
}

fn get_permissions(role: Role): Permissions {
    switch (role) {
        case .Admin:
            return admin_permissions;
        case .User:
            return user_permissions;
        case .Guest:
            return guest_permissions;
    }
}
```

### Anti-Pattern 5: Catching and Ignoring

```nv
// Bad
do {
    try risky_operation();
} catch (e) {
    // Ignoring error
}

// Good: Handle or log
do {
    try risky_operation();
} catch (e) {
    log_error(e);
    return default_value;
}
```

## Performance Patterns

### Lazy Initialization

```nv
struct ExpensiveResource {
    data: Data?,
}

impl ExpensiveResource {
    pub fn new(): ExpensiveResource {
        return ExpensiveResource { data: nil };
    }

    pub fn get_data(self): Data {
        if (self.data == nil) {
            self.data = load_expensive_data();
        }
        return self.data!;
    }
}
```

### Object Pool Pattern

```nv
struct ConnectionPool {
    connections: [Connection],
    max_size: int,
}

impl ConnectionPool {
    pub fn new(max_size: int): ConnectionPool {
        return ConnectionPool {
            connections: [],
            max_size,
        };
    }

    pub fn acquire(self): Connection throws {
        if (self.connections.len() > 0) {
            return self.connections.pop()!;
        }

        if (self.total_connections() < self.max_size) {
            return try Connection.new();
        }

        throw "Pool exhausted";
    }

    pub fn release(self, conn: Connection) {
        self.connections.push(conn);
    }
}
```

## Best Practices Summary

1. **Use optional types** instead of nil checks
2. **Prefer try?** over try! for error handling
3. **Use defer** for resource cleanup
4. **Keep functions small** and focused
5. **Use newtypes** for type safety
6. **Handle errors explicitly** don't swallow them
7. **Use channels** for concurrent communication
8. **Avoid deep nesting** with early returns
9. **Make illegal states unrepresentable** with types
10. **Test public interfaces** not implementation details
