# Kori MEV Proto

This repository contains Protocol Buffers (proto) files for the gRPC services used by the Kori Block Engine, Sui validators and searchers.

## Table of Contents

- [Introduction](#introduction)
- [Proto Files](#proto-files)
- [Installation](#installation)
- [Usage](#usage)
- [Contributing](#contributing)

## Introduction

The Kori MEV Proto repository provides the necessary proto files to interact with the MEV services on the Sui blockchain. These services allow MEV searchers to subscribe to mempool transactions, send transaction bundles, and get results from auction processes. 

## Proto Files

The proto files included in this repository define the following services and messages:

1. **auth.proto**
    - `Role`
    - `GenerateAuthChallengeRequest`
    - `GenerateAuthChallengeResponse`
    - `GenerateAuthTokensRequest`
    - `Token`
    - `GenerateAuthTokensResponse`
    - `RefreshAccessTokenRequest`
    - `RefreshAccessTokenResponse`
    - `AuthService`

2. **block_engine.proto**
   - `SubscribeBundlesRequest`
   - `BlockEngineValidator`

3. **dto.proto**:
    - `MempoolTransactionWithEffects`
    - `MempoolTransactionsWithEffects`
    - `Transaction`
    - `CertifiedTransaction`
    - `Bundle`
    - `CertifiedBundle`

4. **searcher.proto**:
    - `PackageSubscriptionV0`
    - `SharedObjectSubscriptionV0`
    - `AddressSubscriptionV0`
    - `MempoolSubscription`
    - `SendBundleResponse`
    - `GetTipAddress`
    - `GetTipAddressResponse`
    - `SubscribeBundleResultsRequest`
    - `BundleResultAuctionWin`
    - `BundleResultAuctionLoose`
    - `BundleResultPartiallyProcessed`
    - `BundleResultPartiallyExpired`
    - `BundleResultInterrupted`
    - `BundleResultAuctionFailedEstimate`
    - `BundleResultInternalError`
    - `BundleResultAuctionFailed`
    - `BundleResult`
    - `SearcherService`

## Installation

To use these proto files in your project, follow these steps:

1. **Clone the Repository**:
    ```bash
    git clone https://github.com/kori-labs/kori-mev-proto.git
    ```

2. **Install Protocol Buffers Compiler**:
   Follow the instructions on the [Protocol Buffers GitHub page](https://github.com/protocolbuffers/protobuf) to install the `protoc` compiler for your operating system.

3. **Generate Code**:
   Use the `protoc` compiler to generate code in your desired language. For example, to generate Python code:
   ```bash
   protoc -I=. --python_out=. searcher.proto dto.proto
   ```

## Usage

After generating the code, you can import the generated classes into your project and use them to interact with the Sui MEV services. Here is an example in Python:

```python
from generated.searcher_pb2 import MempoolSubscription, PackageSubscriptionV0
from generated.searcher_pb2_grpc import SearcherServiceStub

import grpc

# Create a channel and a stub (client)
channel = grpc.insecure_channel('localhost:50051')
stub = SearcherServiceStub(channel)

# Create a subscription request
subscription = MempoolSubscription(
    package_subscription=PackageSubscriptionV0(package=["package1", "package2"])
)

# Call the SubscribeMempool method
response = stub.SubscribeMempool(subscription)
for tx_with_effects in response:
    print(tx_with_effects)
```

## Authentication Flow (Optional for searchers)

The authentication flow involves the following steps:

1. **Requesting an Authentication Challenge**:
   - Client requests an authentication challenge by supplying its public key.
   - **Message**: `GenerateAuthChallengeRequest`
   - **RPC Method**: `GenerateAuthChallenge`

2. **Generating the Challenge**:
   - Server generates a challenge
   - **Message**: `GenerateAuthChallengeResponse`

3. **Signing the Challenge**:
   - Client signs the challenge and sends it back to the server.
   - **Message**: `GenerateAuthTokensRequest`
   - **RPC Method**: `GenerateAuthTokens`

4. **Generating Tokens**:
   - Server verifies the signature and generates access & refresh tokens.
   - **Message**: `GenerateAuthTokensResponse`

5. **Refreshing the Access Token**:
   - After the access token expires, the client uses the refresh token to generate a new access token.
   - **Message**: `RefreshAccessTokenRequest`
   - **RPC Method**: `RefreshAccessToken`
   - If the refresh token expires, the client must restart the authentication process.

## Contributing

Contributions are welcome! Please open an issue or submit a pull request with any changes or enhancements. Follow these steps to contribute:

1. Fork the repository.
2. Create a new branch for your feature or bugfix.
3. Make your changes and commit them with clear and descriptive messages.
4. Push your changes to your fork.
5. Open a pull request to the main repository.