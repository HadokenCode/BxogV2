# Bxog v.2

Based on the Bxog, more comfortable because of the context of use, but at a slower router

[![API documentation](https://godoc.org/github.com/claygod/BxogV2?status.svg)](https://godoc.org/github.com/claygod/BxogV2)
[![Go Report Card](https://goreportcard.com/badge/github.com/claygod/BxogV2)](https://goreportcard.com/report/github.com/claygod/BxogV2)
# Usage

An example of using the multiplexer:
```go
package main

import (
	bx "github.com/claygod/BxogV2"
	"io"
	"net/http"
)

// Handlers
func IHandler(w http.ResponseWriter, req *http.Request) {
	io.WriteString(w, "Welcome to Bxog!")
}
func THandler(w http.ResponseWriter, req *http.Request) {
	io.WriteString(w, "Params:\n")
	if x := (req.Context().Value("par")).(string); x != "" {
		io.WriteString(w, " 'par' -> "+x+"\n")
	}
}
func PHandler(w http.ResponseWriter, req *http.Request) {
	// Getting parameters
	io.WriteString(w, "Country:\n")
	io.WriteString(w, " 'name' -> "+(req.Context().Value("name")).(string)+"\n")
	io.WriteString(w, " 'capital' -> "+(req.Context().Value("city")).(string)+"\n")
	io.WriteString(w, " 'valuta' -> "+(req.Context().Value("money")).(string)+"\n")

}

// Main
func main() {
	m := bx.New()
	m.Add("/", IHandler)
	m.Add("/abc/:par", THandler)
	m.Add("/country/:name/capital/:city/valuta/:money", PHandler).
		Id("country"). // For ease indicate the short ID
		Method("GET")  // GET method do not need to write here, it is used by default (this is an example)
	m.Start(":80")
}
```

Click URLs:
- http://localhost
- http://localhost/abc/123
- http://localhost/country/USA/capital/Washington/valuta/dollar

# Settings

Necessary changes in the configuration of the multiplexer can be made in the configuration file [config.go](https://github.com/claygod/Bxog/blob/master/config.go)

# Perfomance

Bxog is the fastest router, showing the speed of query processing. Its speed is comparable to the speed of the popular multiplexers: Bone, Httprouter, Gorilla, Zeus.  Detailed benchmark [here](https://github.com/claygod/bxogtest). In short (less time, the better):

- Bxog         330 ns/op
- HttpRouter   395 ns/op
- Zeus       23772 ns/op
- GorillaMux 30223 ns/op
- GorillaPat  1253 ns/op
- Bone       63656 ns/op

# API

Methods:
-  *New* - create a new multiplexer
-  *Add* - add a rule specifying the handler (the default method - GET, ID - as a string to this rule)
-  *Start* - start the server indicating the listening port
-  *Test* - Start analogue (for testing only)

Example:
`
	m := bxog.New()
	m.Add("/", IHandler)
`

# Named parameters

Arguments in the rules designated route colon. Example route: */abc/:param* , where *abc* is a static section and *:param* - the dynamic section(argument).
The parameters are transmitted via `context`

# Static files

The directory path to the file and its nickname as part of URL specified in the configuration file. This constants *FILE_PREF* and *FILE_PATH*
