# ratelimit
--
    import "github.com/juju/ratelimit"

The ratelimit package provides an efficient token bucket implementation. See
http://en.wikipedia.org/wiki/Token_bucket.

## Usage

#### func  Reader

```go
func Reader(r io.Reader, bucket *Bucket) io.Reader
```
Reader returns a reader that is rate limited by the given token bucket. Each
token in the bucket represents one byte.

#### func  Writer

```go
func Writer(w io.Writer, bucket *Bucket) io.Writer
```
Writer returns a reader that is rate limited by the given token bucket. Each
token in the bucket represents one byte.

#### type Bucket

```go
type Bucket struct {
}
```

Bucket represents a token bucket that fills at a predetermined rate. Methods on
Bucket may be called concurrently.

#### func  NewBucket

```go
func NewBucket(fillInterval time.Duration, capacity int64) *Bucket
```
NewBucket returns a new token bucket that fills at the rate of one token every
fillInterval, up to the given maximum capacity. Both arguments must be positive.
The bucket is initially full.

#### func  NewBucketWithRate

```go
func NewBucketWithRate(rate float64, capacity int64) *Bucket
```
NewBucketWithRate returns a token bucket that fills the bucket at the rate of
rate tokens per second up to the given maximum capacity. Because of limited
clock resolution, at high rates, the actual rate may be up to 1% different from
the specified rate.

#### func (*Bucket) Rate

```go
func (tb *Bucket) Rate() float64
```
Rate returns the fill rate of the bucket, in tokens per second.

#### func (*Bucket) Take

```go
func (tb *Bucket) Take(count int64) time.Duration
```
Take takes count tokens from the bucket without blocking. It returns the time
that the caller should wait until the tokens are actually available.

Note that if the request is irrevocable - there is no way to return tokens to
the bucket once this method commits us to taking them.

#### func (*Bucket) TakeAvailable

```go
func (tb *Bucket) TakeAvailable(count int64) int64
```
TakeAvailable takes up to count immediately available tokens from the bucket. It
returns the number of tokens removed, or zero if there are no available tokens.
It does not block.

#### func (*Bucket) Wait

```go
func (tb *Bucket) Wait(count int64)
```
Wait takes count tokens from the bucket, waiting until they are available.
