# go-cryptkeeper
> Golang library, wrapping encryption and decryption for use as a database type and/or json.

## Install

```
$ go get github.com/blaskovicz/go-cryptkeeper
```

## Use

```go
import (
  "database/sql
  "github.com/blaskovicz/go-cryptkeeper/types"
)

// set env variable, used for encryption, to a 16, 24, or 32 byte value
os.Setenv("CRYPT_KEEPER_KEY", "12345678901234567890123456789012")

db, err := sql.Open("postgres", os.Getenv("DATABASE_URL"))
if err != nil {
  panic(err)
}

// declare a struct for database Value
cs := types.CryptString{"hello word"}

// insert the row; uses types.Encrypt(string) under the covers.
// column type can be something like text, varchar, or bytea
_, err = db.Exec("INSERT INTO secret_phrases(phrase) VALUES ($1)", &cs)
if err != nil {
  panic(err)
}

// declare a struct for database Scan
var cs2 types.CryptString

// select the inserted row; uses types.Decrypt(string) under the covers.
err = db.QueryRow("SELECT phrase FROM secret_phrases LIMIT 1").Scan(&cs2)
if err != nil {
  panic(err)
}

if cs2.String != cs.String {
  panic(fmt.Sprintf("Expected %s, got %s", cs.String, cs2.String))
}
```

## Test

```
$ go test ./...
```

## Hacking

Pull requests welcome!