# Concurrency in Navi

Guide to concurrent programming in Navi using `spawn` and channels.

## Important: Concurrency vs Parallelism

**Navi provides concurrency, NOT parallelism:**
- Tasks run in a **single thread**
- Tasks **interleave execution** but don't run simultaneously
- Similar to JavaScript async/await or Go with GOMAXPROCS=1
- Blocking operations block the entire runtime

```
Concurrency (Navi):     Parallelism (not Navi):
Task1: ----▓▓--▓▓----   Task1: ▓▓▓▓▓▓▓▓▓▓
Task2: --▓▓--▓▓--▓▓--   Task2: ▓▓▓▓▓▓▓▓▓▓
       Single thread    Multiple threads
```

## Spawn

### Basic Spawn

```nv
use std.time;

fn main() throws {
    spawn {
        println("Task 1");
        time.sleep(0.1.seconds());
        println("Task 1 done");
    }

    spawn {
        println("Task 2");
        time.sleep(0.1.seconds());
        println("Task 2 done");
    }

    println("Main continues");

    time.sleep(0.2.seconds());  // Wait for tasks
}
```

### Spawn with Defer

```nv
spawn {
    defer {
        println("Cleanup");
    }

    println("Working");
    // defer runs when spawn block exits
}
```

## Channels

### Creating Channels

```nv
// Typed channel
let ch = channel::<int>();
let ch: channel<string> = channel();
```

### Sending and Receiving

```nv
fn main() throws {
    let ch = channel::<int>();

    spawn {
        try! ch.send(42);
        try! ch.send(100);
    }

    let val1 = try ch.recv();  // 42
    let val2 = try ch.recv();  // 100

    println(`Received: ${val1}, ${val2}`);
}
```

### Channel Operations

```nv
// Send (can throw if channel closed)
try ch.send(value);
try! ch.send(value);  // Panic on error

// Receive (can throw if channel closed or empty)
let value = try ch.recv();
let value = try! ch.recv();

// Try receive (non-blocking, returns optional)
let value = try? ch.recv();  // nil if no data
```

## Common Patterns

### Pattern 1: Worker Pool

```nv
fn worker_pool(jobs: [int]) throws {
    let results = channel::<int>();

    // Spawn workers
    for (let job in jobs) {
        spawn {
            let result = process_job(job);
            try! results.send(result);
        }
    }

    // Collect results
    for (let i in 0..jobs.len()) {
        let result = try results.recv();
        println(`Result ${i}: ${result}`);
    }
}
```

### Pattern 2: Fan-Out

```nv
fn fan_out(data: [int]) throws {
    let results = channel::<int>();

    for (let item in data) {
        spawn {
            let processed = process(item);
            try! results.send(processed);
        }
    }

    // Collect all results
    let collected: [int] = [];
    for (let i in 0..data.len()) {
        collected.push(try results.recv());
    }

    return collected;
}
```

### Pattern 3: Pipeline

```nv
fn pipeline(input: [int]) throws {
    let stage1 = channel::<int>();
    let stage2 = channel::<string>();

    // Stage 1: Process numbers
    spawn {
        for (let n in input) {
            let result = n * 2;
            try! stage1.send(result);
        }
    }

    // Stage 2: Convert to strings
    spawn {
        for (let i in 0..input.len()) {
            let n = try! stage1.recv();
            let s = n.to_string();
            try! stage2.send(s);
        }
    }

    // Collect final results
    let results: [string] = [];
    for (let i in 0..input.len()) {
        results.push(try stage2.recv());
    }

    return results;
}
```

### Pattern 4: Timeout Pattern

```nv
use std.time;

fn with_timeout(operation: |(): int|, timeout: Duration): int? {
    let result = channel::<int?>();

    spawn {
        let val = operation();
        try! result.send(val);
    }

    spawn {
        time.sleep(timeout);
        try! result.send(nil);
    }

    return try! result.recv();
}
```

### Pattern 5: Synchronization

```nv
fn synchronized_work() throws {
    let done = channel::<bool>();

    spawn {
        // Do work
        println("Working...");
        time.sleep(0.1.seconds());

        // Signal completion
        try! done.send(true);
    }

    // Wait for completion
    try done.recv();
    println("Work completed");
}
```

## Best Practices

### DO: Use Channels for Communication

```nv
// Good: Channel-based communication
let results = channel::<Result>();

spawn {
    let result = compute();
    try! results.send(result);
}

let result = try results.recv();
```

### DON'T: Block the Runtime

```nv
// Bad: Blocks entire runtime
spawn {
    while (true) {
        // Busy loop blocks everything
    }
}

// Good: Use time.sleep for delays
spawn {
    while (running) {
        do_work();
        time.sleep(0.1.seconds());  // Yields control
    }
}
```

### DO: Clean Up with Defer

```nv
spawn {
    let resource = acquire_resource();

    defer {
        release_resource(resource);
    }

    // Use resource
}
```

### DON'T: Share Mutable State

```nv
// Bad: Shared mutable state (no mutex in Navi)
let counter = 0;

spawn {
    counter += 1;  // Race condition
}

spawn {
    counter += 1;  // Race condition
}

// Good: Use channels
let counter = channel::<int>();

spawn {
    try! counter.send(1);
}

spawn {
    try! counter.send(1);
}

let total = try! counter.recv() + try! counter.recv();
```

### DO: Handle Channel Errors

```nv
// Good: Handle errors
let result = try? ch.recv();
if (let r = result) {
    process(r);
} else {
    println("Channel closed or empty");
}

// Or propagate
let result = try ch.recv();
```

## Common Pitfalls

### Pitfall 1: Expecting Parallelism

```nv
// This doesn't run in parallel!
spawn {
    compute_intensive_task();  // Blocks entire runtime
}

spawn {
    other_task();  // Waits for first task
}
```

### Pitfall 2: Forgetting to Receive

```nv
// Bad: Sender waits forever
let ch = channel::<int>();

spawn {
    try! ch.send(42);  // Blocks if no receiver
}

// Forgot to receive!
// Good: Always receive
let value = try ch.recv();
```

### Pitfall 3: Not Waiting for Completion

```nv
// Bad: Main exits before spawn completes
fn main() throws {
    spawn {
        println("This might not print");
    }
    // main exits immediately
}

// Good: Wait for completion
fn main() throws {
    let done = channel::<bool>();

    spawn {
        println("This will print");
        try! done.send(true);
    }

    try done.recv();  // Wait
}
```

## Select Statement (Future Feature)

Note: Select is mentioned in keywords but may not be fully implemented.

```nv
// Conceptual usage (may not work yet)
select {
    case let value = ch1.recv():
        println(`From ch1: ${value}`);
    case let value = ch2.recv():
        println(`From ch2: ${value}`);
    default:
        println("No data available");
}
```

## Performance Considerations

### Spawn Overhead

- Spawning tasks has minimal overhead
- Tasks are lightweight (not OS threads)
- Can spawn thousands of tasks

### Channel Performance

- Channels are efficient for communication
- Prefer channels over shared state
- Batch operations when possible

### When to Use Concurrency

**Good use cases:**
- I/O-bound operations (network, file)
- Waiting for multiple independent operations
- Event-driven architectures
- Producer-consumer patterns

**Poor use cases:**
- CPU-intensive calculations (no parallel execution)
- Tight loops (consider batching)
- When sequential is simpler and sufficient

## Testing Concurrent Code

```nv
test "concurrent operations" {
    let ch = channel::<int>();

    spawn {
        try! ch.send(42);
    }

    let result = try! ch.recv();
    assert_eq result, 42;
}

test "multiple workers" {
    let results = channel::<int>();

    for (let i in 0..3) {
        spawn {
            try! results.send(i * 2);
        }
    }

    let sum = 0;
    for (let i in 0..3) {
        sum += try! results.recv();
    }

    assert_eq sum, 6;  // 0 + 2 + 4
}
```
