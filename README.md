# fcrypto

File encryption/decryption extracted from Nick Craig-Wood's [rclone source code](https://github.com/ncw/rclone).

> rclone uses nacl secretbox which in turn uses XSalsa20 and Poly1305 to encrypt and authenticate your configuration with secret-key cryptography. The password is SHA-256 hashed, which produces the key for secretbox. The hashed password is not stored.
>
> While this provides very good security, we do not recommend storing your encrypted rclone configuration in public if it contains sensitive information, maybe except if you use a very strong password.

See [file encryption in rclone docs.](https://github.com/ncw/rclone/blob/976a020a2f4814ab32686bd47870ddb45699950a/docs/content/docs.md)

```go
package main

import (
	"fmt"
	"os"

	"github.com/rubiojr/fcrypto"
)

const secret = "bar"
const file = "secret.conf"

func main() {
	needsSave := false

	if _, err := os.Stat(file); os.IsNotExist(err) {
		fmt.Println("Creating an empty file and adding 'foobar' to it")
		needsSave = true
		fcrypto.CreateFile(file, secret)
	} else {
		fmt.Println("Loading existing " + file)
	}

	buf, err := fcrypto.LoadFile(file, secret)
	if err != nil {
		panic(err)
	}

	if needsSave {
		buf.Write([]byte("foobar"))
		fmt.Printf("Encrypting the file %s with password '%s'\n", file, secret)
		err := fcrypto.SaveFile(buf, file, secret)
		if err != nil {
			fmt.Println(err)
		}
	} else {
		fmt.Println(buf.String())
	}
}

```