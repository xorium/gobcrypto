# bcrypt
--
    import "."

Package bcrypt implements Provos and Mazières's bcrypt adaptive hashing
algorithm. See http://www.usenix.org/event/usenix99/provos/provos.pdf

## Usage
### Example
```go
var password = "pass123"
var salt = "faf6132ae848fkjf" // 16 byte!

passHash, err := GenerateFromPasswordAndSalt([]byte(password), cost, []byte(salt))
if err != nil {
	log.Fatalf(
		"Error when generating hash for password %s with salst %s, cost=%d: %v"
	    password, salt, cost, err,
	)
}

if err := CompareHashAndPassword(passHash, []byte(password)); err != nil {
	log.Fatalf("can't compare hash %s ans password %s: %v", string(passHash), password, err)
}
```

### Constants
```go
const (
	MinCost     int = 4  // the minimum allowable cost as passed in to GenerateFromPassword
	MaxCost     int = 31 // the maximum allowable cost as passed in to GenerateFromPassword
	DefaultCost int = 10 // the cost that will actually be set if a cost below MinCost is passed into GenerateFromPassword
)
```

### Globals (errors)
```go
var ErrHashTooShort = errors.New("crypto/bcrypt: hashedSecret too short to be a bcrypted password")
```
ErrHashTooShort is the error returned from CompareHashAndPassword when a hash is
too short to be a bcrypt hash.

```go
var ErrMismatchedHashAndPassword = errors.New("crypto/bcrypt: hashedPassword is not the hash of the given password")
```
ErrMismatchedHashAndPassword is the error returned from CompareHashAndPassword
when a password and hash do not match.

### Functions

#### func  CompareHashAndPassword

```go
func CompareHashAndPassword(hashedPassword, password []byte) error
```
CompareHashAndPassword compares a bcrypt hashed password with its possible
plaintext equivalent. Returns nil on success, or an error on failure.

#### func  Cost

```go
func Cost(hashedPassword []byte) (int, error)
```
Cost returns the hashing cost used to create the given hashed password. When, in
the future, the hashing cost of a password system needs to be increased in order
to adjust for greater computational power, this function allows one to establish
which passwords need to be updated.

#### func  GenerateFromPassword

```go
func GenerateFromPassword(password []byte, cost int) ([]byte, error)
```
GenerateFromPassword returns the bcrypt hash of the password at the given cost.
If the cost given is less than MinCost, the cost will be set to DefaultCost,
instead. Use CompareHashAndPassword, as defined in this package, to compare the
returned hashed password with its cleartext version.

#### func  GenerateFromPasswordAndSalt

```go
func GenerateFromPasswordAndSalt(password []byte, cost int, salt []byte) ([]byte, error)
```
GenerateFromPasswordAndSalt lets generate hash from password, cost (number of
rounds) and 16 bytes of a salt.

#### type HashVersionTooNewError

```go
type HashVersionTooNewError byte
```

HashVersionTooNewError is the error returned from CompareHashAndPassword when a
hash was/created with a bcrypt algorithm newer than this implementation.

#### func (HashVersionTooNewError) Error

```go
func (hv HashVersionTooNewError) Error() string
```

#### type InvalidCostError

```go
type InvalidCostError int
```


#### func (InvalidCostError) Error

```go
func (ic InvalidCostError) Error() string
```

#### type InvalidHashPrefixError

```go
type InvalidHashPrefixError byte
```

InvalidHashPrefixError is the error returned from CompareHashAndPassword when a
hash starts with something other than '$'

#### func (InvalidHashPrefixError) Error

```go
func (ih InvalidHashPrefixError) Error() string
```
