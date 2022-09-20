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

Inside config file you can specify which tables from database you want to process.
```
"messages": [
        "msg_instantiate_contracts", "msg_execute_contracts"
    ]
}
```


## Graphql for contract messages
Subquery won't catch new entities, but we can start new graphql server for hosting them all from database. Inside `juno-contracts-indexer` you can start hosting graphql server with:
```
yarn
yarn build
yarn watch
```

## Important note
If you have database connection error, please edit the last line from file `juno-dao-contract/.data/postgres/pg_hba.conf`:
```
host  all  all 0.0.0.0/0 md5
```
To:
```
host  all  all 0.0.0.0/0 trust
```
After saving changes rerun subquery docker with command `yarn start:docker`


Example query:
```
query MyQuery {
  msgInstantiateContracts {
    nodes {
      height
      hash
      txHash
      index
      micontract43ByMicontract43 {
        description
        mic43ThresholdByMic43Threshold {
          mic43TthresholdquorumByMic43Tthresholdquorum {
            quorum
            threshold
          }
        }
        refundFailedProposal
        proposalDepositAmount
        name
        mic43GovtokenByMic43Govtoken {
          mic43Gtincw20ByMic43Gtincw20 {
            cw20CodeId
            initialDaoBalance
            label
            stakeContractCodeId
          }
        }
      }
    }
  }
}

```

Result:
```
{
  "data": {
    "msgInstantiateContracts": {
      "nodes": [
        {
          "height": "4152471",
          "hash": "ACA092AA1BB8501AD6AAFE796C40BBCC92FD8697379607CE1CD32D2B53CDD198",
          "txHash": "917AE5495D322DC485B6772AAD9DB457C51B8F7EA2BDBE15734265E054680FA6",
          "index": 0,
          "micontract43ByMicontract43": {
            "description": "In Development",
            "mic43ThresholdByMic43Threshold": {
              "mic43TthresholdquorumByMic43Tthresholdquorum": {
                "quorum": "0.33",
                "threshold": "0.51"
              }
            },
            "refundFailedProposal": true,
            "proposalDepositAmount": "0",
            "name": "New Age Kingz",
            "mic43GovtokenByMic43Govtoken": {
              "mic43Gtincw20ByMic43Gtincw20": {
                "cw20CodeId": "37",
                "initialDaoBalance": "89999999",
                "label": "New Age Koin",
                "stakeContractCodeId": "41"
              }
            }
          }
        }
      ]
    }
  }
}
```


