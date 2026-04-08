---
title: Defer vs EOF calls in Go
date: 2025-02-20
tags:
  - go
comment: Edgey casey!
---

## Key Differences:

1. **Execution Order**

```go
func deferExample() {
    defer fmt.Println("1")
    defer fmt.Println("2")
    defer fmt.Println("3")
}
// Output: 3, 2, 1 (LIFO - Last In First Out)

func normalExample() {
    fmt.Println("1")
    fmt.Println("2")
    fmt.Println("3")
}
// Output: 1, 2, 3 (Sequential)
```

2. **Error Handling**

```go
func withDefer() {
    f, err := os.Open("file.txt")
    if err != nil {
        return // file will never be closed
    }
    defer f.Close() // guaranteed to run even if error occurs later
    // ... work with file
}

func withoutDefer() {
    f, err := os.Open("file.txt")
    if err != nil {
        return // file will never be closed
    }
    // ... work with file
    f.Close() // might not be reached if panic or early return occurs
}
```

3. **Early Returns**

```go
func withDefer(x int) {
    defer fmt.Println("cleanup")
    if x < 0 {
        return // cleanup still runs
    }
    // ... more code
}

func withoutDefer(x int) {
    if x < 0 {
        fmt.Println("cleanup")
        return // need to duplicate cleanup code
    }
    // ... more code
    fmt.Println("cleanup")
}
```

Key advantages of `defer`:

- Guaranteed execution (even during panics)
- Cleanup code stays close to resource acquisition
- Handles multiple return points elegantly
- Perfect for cleanup operations (closing files, mutexes, etc.)

When to use each:

- Use `defer` for cleanup operations and guaranteed execution
- Use end-of-function calls for sequential operations where order matters
