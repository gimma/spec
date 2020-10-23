# General overview

The gimma project is going to build a standard way of building (micro)services using Go. The main goal of the project is to abstract as much infrastructure-related code as it's possible to give developers the possibility to focus only what's matter - the business logic.

## The motivation

I've noticed that we have a tendency to reinventing the wheel over and over. This is not necessary because a more and more things in our envirement is going to be the same or being very similar.

One of those things is exposing readiness and healthiness probes. It's very common thanks to Kubernetes, well definied and standarized. As well as [logging and metrics](https://opentelemetry.io/), [generating documentation](https://swagger.io/) etc.

The project is not going to resolve all issues but as much as it's possible to take the responsibility from the developer.

# The general concept

## Using the CLI

### Creating a new project

```
gimma new projectName
```

### Running tests

```
gimma test
```

The following command setups everything's needed to run tests: download dependencies and run services like a database (run DB migrations etc) and executes tests. When the process is finished, it tears everything down.

### Run project locally

```
gimma run
```

The following command setups everything's needed to run the application: download dependencies and run services like a database (run DB migrations etc) and runs the application. When the process is finished, it tears everything down.

## Generating API docs

## Communicating with services
### Interal API

### Public API

Let's assume that the developer prepared a function that takes parameters and returns and the result following by an optional error.

```go
func MyLogic(param1 int, param2 string) error {
   // logic goes
}
```

The function represents the business requrement. To expose the function to be accesable by HTTP endpoint a proper annotation should be added.


```go
// api:public
func MyLogic(param1 int, param2 string) error {
   // logic goes
}
```

The annotation means that this function is a part of a public API. Gimma, based on the provided information is able to generate the HTTP handler with the whole boilerplate for the developer on the fly (the code below is a draft, not actual proposal).

```go
type myLogicHandler struct {
 logger logger
}

type myLogicRequest struct {
 Param1 int
 Param2 string
}

func (h myLogicHandler)HandleMyLogic(w http.ResponseWriter, r *http.Request) {
  body, err := ioutil.ReadAll(r.Body)
  if err != nil {
   h.logger.Error(err)
    badRequestErr(w, r)
   return
  }

  params := myLogicRequest{}
  err = json.Unmarshal(body, &params)
  if err != nil {
   h.logger.Error(err)
   badRequestErr(w, r)
   return
  }

  if err = validateMyLogicParams(params); err != nil {
    h.logger.Error(err)
    validationErr(w, r)
    return
  }

  if err = MyLogic(params.Param1, params.Param2); err != nil {
    h.logger.Error(err)
    internalServerErr(w, r)
    return
  }

  resultOK(w, r)
  return
}
```

## Connecting to a database

## Logging

## Exposing readiness and helthiness probes
