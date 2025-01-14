# Our Doc

This is a fork of [gqlgenc](https://github.com/Yamashou/gqlgenc). We have made custom changes to
- Handle BigFloat
- Handle nested types
- Allow injecting custom GraphQL client

The changes we made can be viewed [here](https://github.com/Yamashou/gqlgenc/compare/master...TripleMint:master)

## How to use this package

Run `go get github.com/TripleMint/gqlgenc` to install the executable and update your `go.mod` and `go.sum` files.

To prevent `go mod tidy` from removing this library, add a `tools.go` file with the following code:
```
// +build tools

package tools

import (
	_ "github.com/TripleMint/gqlgenc"
)
```

Add a `.gqlgenc.yml` configuration file in the directory where queries are stored.
Then run `GRAPHQL_HOST=$HOST gqlgenc`. You should see files generated in the directories you have specified in the configuration file.



# Upstream Repo Doc

The rest of this doc is from the original repo.

This is a fork of [gqlgenc](https://github.com/Yamashou/gqlgenc). We have made custom changes to
- Handle BigFloat
- Handle nested types
- Allow injecting custom GraphQL client

The changes we made can be viewed [here](https://github.com/Yamashou/gqlgenc/compare/master...TripleMint:master)
or as [diff](https://github.com/Yamashou/gqlgenc/compare/master...TripleMint:master.diff).

The rest of this doc is from the original repo.

## What is gqlgenc ?

This is Go library for building GraphQL client with [gqlgen](https://github.com/99designs/gqlgen)

## Motivation

Now, if you build GraphQL api client for Go, have choice:

 - [github.com/shurcooL/graphql](https://github.com/shurcooL/graphql)
 - [github.com/machinebox/graphql](https://github.com/machinebox/graphql)

These libraries are very simple and easy to handle.
However, as I work with [gqlgen](https://github.com/99designs/gqlgen) and [graphql-code-generator](https://graphql-code-generator.com/) every day, I find out the beauty of automatic generation.
So I want to automatically generate types.

## Installation

```shell script
go get -u github.com/TripleMint/gqlgenc
```

## How to use

### Client Codes Only

gqlgenc base is gqlgen with [plugins](https://gqlgen.com/reference/plugins/). So the setting is yaml in each format.
gqlgenc can be configured using a `.gqlgenc.yml` file

Load a schema from a remote server:

```yaml
model:
  package: generated
  filename: ./models_gen.go # https://github.com/99designs/gqlgen/tree/master/plugin/modelgen
client:
  package: generated
  filename: ./client.go # Where should any generated client go?
models:
  Int:
    model: github.com/99designs/gqlgen/graphql.Int64
  Date:
    model: github.com/99designs/gqlgen/graphql.Time
endpoint:
  url: https://api.annict.com/graphql # Where do you want to send your request?
  headers:　# If you need header for getting introspection query, set it
    Authorization: "Bearer ${ANNICT_KEY}" # support environment variables
query:
  - "./query/*.graphql" # Where are all the query files located?
```

Load a schema from a local file:

```yaml
model:
  package: generated
  filename: ./models_gen.go # https://github.com/99designs/gqlgen/tree/master/plugin/modelgen
client:
  package: generated
  filename: ./client.go # Where should any generated client go?
models:
  Int:
    model: github.com/99designs/gqlgen/graphql.Int64
  Date:
    model: github.com/99designs/gqlgen/graphql.Time
schema:
  - "schema/**/*.graphql" # Where are all the schema files located?
query:
  - "./query/*.graphql" # Where are all the query files located?
```

Execute the following command on same directory for .gqlgenc.yml

```shell script
gqlgenc
```

### With gqlgen

Do this when creating a server and client for Go.
You create your own entrypoint for gqlgen.
This use case is very useful for testing your server.


```go
package main

import (
	"fmt"
	"os"

	"github.com/TripleMint/gqlgenc/clientgen"

	"github.com/99designs/gqlgen/api"
	"github.com/99designs/gqlgen/codegen/config"
)

func main() {
	cfg, err := config.LoadConfigFromDefaultLocations()
	if err != nil {
		fmt.Fprintln(os.Stderr, "failed to load config", err.Error())
		os.Exit(2)
	}
	queries := []string{"client.query", "fragemt.query"}
	clientPackage := config.PackageConfig{
		Filename: "./client.go",
		Package:  "gen",
	}

	clientPlugin := clientgen.New(queries, clientPackage)
	err = api.Generate(cfg,
		api.AddPlugin(clientPlugin),
	)
	if err != nil {
		fmt.Fprintln(os.Stderr, err.Error())
		os.Exit(3)
	}
}
```

## Documents

- [How to configure gqlgen using gqlgen.yml](https://gqlgen.com/config/)
- [How to write plugins for gqlgen](https://gqlgen.com/reference/plugins/)


## Comments

### Japanese Comments
These codes have Japanese comments. Replace with English.

### Subscription

This client does not support subscription. If you need a subscription, please create an issue or pull request.

### Pre-conditions

[clientgen](https://github.com/TripleMint/gqlgenc/tree/master/clientgen) is created based on [modelgen](https://github.com/99designs/gqlgen/tree/master/plugin/modelgen). So if you don't have a modelgen, it may be a mysterious move.
