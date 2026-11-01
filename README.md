# Go Worker Pool

A bounded-concurrency worker pool built with goroutines and channels. It limits concurrent work, applies backpressure when its queue is full, and supports graceful shutdown.

## Run tests

```bash
go test -race ./...
```

## Example

```go
p := pool.New(context.Background(), 4, 20)
err := p.Submit(context.Background(), func(ctx context.Context) error {
    // Perform work and respect ctx cancellation.
    return nil
})
if err != nil {
    log.Fatal(err)
}

shutdownCtx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
defer cancel()
if err := p.Shutdown(shutdownCtx); err != nil {
    log.Printf("shutdown: %v", err)
}
```

Import the package with `github.com/Servitor108/go-worker-pool/pool`.

## Engineering concepts

- Fixed goroutine count bounds concurrent execution.
- Buffered channel provides a bounded queue and backpressure.
- Submit supports caller cancellation.
- Shutdown stops submissions, drains accepted jobs, and can cancel work on timeout.

Job errors are intentionally left to the caller's job wrapper. A production version could add result collection, panic recovery, metrics, and explicit error handling.
