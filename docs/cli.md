# Agent CLI

The CLI allows you to perform various tasks related to the computation and management of algorithms and datasets. The CLI is a gRPC client for the agent service.

## Build

To build the CLI, follow these steps:

1. Clone the repository: `go get github.com/ultravioletrs/cocos`.
2. Navigate to the project root: `cd cocos`.
3. Build the CLI binary: `make cli`.

## Usage

#### Set Agent URL
```shell
export AGENT_GRPC_URL=<agent_host:agent_host>
```

#### Upload Algorithm

To upload an algorithm, use the following command:

```bash
./build/cocos-cli algo /path/to/algorithm <algorithm_id> <algorithm_provider>
```

#### Upload Dataset

To upload a dataset, use the following command:

```bash
./build/cocos-cli data /path/to/dataset.csv <dataset_id> <dataset_provider>
```

#### Retrieve Result

To retrieve the computation result, use the following command:
```bash
./build/cocos-cli result <result-consumer>
```

#### Attestation

To retrieve the attestation, use the following command:
```bash
./build/cocos-cli attestation get <report_data>
```

To validate the attestation, use the following:
```bash
./build/cocos-cli attestation validate <attestation_report_file_path>
```

## Installation

To install the CLI locally, i.e. for the current user:

Run `make install-cli`.

## Notes

- The CLI supports various configuration flags and options
- Use the `--help` flag with any command to see additionalinformation
- The CLI uses gRPC for communication with the Agent service
- All traffic between CLI and the TEE is encrypted via mutual TLS
