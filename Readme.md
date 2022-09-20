# Juno Indexer

### Requirements
* [Node](https://nodejs.org/en/download/)
* [Docker](https://docs.docker.com/engine/install/)
* [Go](https://go.dev/doc/install)
* Yarn [`sudo npm install -g yarn`]
* Typescript [`sudo npm install -g typescript`]

Check out all repositories with submodule:
```
git submodule update --recursive --remote --force
```

## Subquery
Deploy Subquery subgraph and start indexing contract messages. Inside `juno-dao-contracts` inside `project.yaml` you can 
update start block value.
```
yarn
yarn codegen
yarn build
yarn start:docker
```

Subquery is exposing GraphQL API on `http://localhost:3000/` when docker is running.

Example query
```
query {
  msgExecuteContracts{
    nodes{
      height
      hash
      txHash
      msg
    }
  }
}
```
Everything inside Execute and Instatiate message comes as decoded JSON in `msg` field. To enable querying this data with 
GraphQL run `juno-smart-contracts` project.

## Smart contract messages
Run smart contract worker inside `juno-smart-contracts` to process contract messages with command:
```
go run cmd/worker/main.go --config config.json
```


## Graphql for contract messages
Inside `juno-contracts-indexer` you can start hosting graphql server for all entites that are inside database with one 
simple command:
```
yarn watch
```

