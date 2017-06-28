# Session

Session Management for QOR

It wrapped other libs like [SCS](https://github.com/alexedwards/scs), [Gorilla Session](http://www.gorillatoolkit.org/pkg/sessions) into a common interface, which will be used for QOR libs and your application.

## Basic Usage

```go
import (
	"github.com/gorilla/sessions"
	"github.com/qor/session/gorilla"
	// "github.com/alexedwards/scs/engine/memstore"
)

var SessionManager = session.ManagerInterface

func main() {
  // Use gorilla session as the backend
	engine := sessions.NewCookieStore([]byte("something-very-secret"))
	SessionManager = gorilla.New("_session", engine)
  // Use SCS as the backend
	// engine := memstore.New(0)
	// SessionManager := scs.New(engine)

  mux := http.NewServeMux()
  mux.HandleFunc("/put", putHandler)
  mux.HandleFunc("/get", getHandler)
  // Your routes

  // Wrap your application's handlers or router with manager's Middleware
  http.ListenAndServe(":7000", manager.Middleware(mux))
}

func putHandler(w http.ResponseWriter, req *http.Request) {
  SessionManager.Add(req, "key", "value")
}

func getHandler(w http.ResponseWriter, req *http.Request) {
  value := SessionManager.Get(req, "key")
  io.WriteString(w, value)
}
```

## Common Interface

```go
type ManagerInterface interface {
  // Add value to session data, if value is not string, will marshal it into JSON encoding and save it into session data.
  Add(req *http.Request, key string, value interface{}) error

  // Get value from session data
  Get(req *http.Request, key string) string

  // Pop value from session data
  Pop(req *http.Request, key string) string

  // Flash add flash message to session data
  Flash(req *http.Request, message Message) error

  // Flashes returns a slice of flash messages from session data
  Flashes(req *http.Request) []Message

  // Load get value from session data and unmarshal it into result
  Load(req *http.Request, key string, result interface{}) error

  // PopLoad pop value from session data and unmarshal it into result
  PopLoad(req *http.Request, key string, result interface{}) error

  // Middleware returns a new session manager middleware instance.
  Middleware(http.Handler) http.Handler
}
```

## License

Released under the [MIT License](http://opensource.org/licenses/MIT).
