# gRPC Node.js Example

This project demonstrates how to create a simple gRPC service in Node.js using Protocol Buffers.

## Requirements

- [Node.js](https://nodejs.org/en/download)
- [Protocol Buffers (protoc)](https://protobuf.dev/downloads/)

## Installation

1. Clone this repository or create a new project folder:
   ```bash
   mkdir grpc-tp && cd grpc-tp
   ```

2. Initialize a new Node.js project:
   ```bash
   npm init -y
   ```

3. Install the required dependencies:
   ```bash
   npm install @grpc/grpc-js @grpc/proto-loader
   ```

## Define the gRPC Service

Create a `hello.proto` file with the following content:

```proto
syntax = "proto3";

package hello;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

## Implement the gRPC Server

Create a `server.js` file with the following code:

```javascript
const grpc = require('@grpc/grpc-js');
const protoLoader = require('@grpc/proto-loader');
const path = require('path');

const PROTO_PATH = path.join(__dirname, 'hello.proto');
const packageDefinition = protoLoader.loadSync(PROTO_PATH, {
  keepCase: true,
  longs: String,
  enums: String,
  defaults: true,
  oneofs: true
});
const helloProto = grpc.loadPackageDefinition(packageDefinition).hello;

function sayHello(call, callback) {
  const { name } = call.request;
  callback(null, { message: `Bonjour, ${name} !` });
}

function main() {
  const server = new grpc.Server();
  server.addService(helloProto.Greeter.service, { SayHello: sayHello });
  const port = '0.0.0.0:50051';
  server.bindAsync(port, grpc.ServerCredentials.createInsecure(), () => {
    console.log(`Server running at ${port}`);
  });
}

main();
```

## Running the gRPC Server

Start the server by running:
```bash
node server.js
```
You should see:
```bash
Server running at 0.0.0.0:50051
```

## Testing with Postman

1. Open [Postman](https://www.postman.com/downloads/)
2. Create a new **gRPC Request**
3. Set the host as `localhost:50051`
4. Import or define the `.proto` file
5. Select `Greeter.SayHello` method
6. Send a request with the following JSON body:
   ```json
   { "name": "TestUser" }
   ```
7. Expected response:
   ```json
   { "message": "Bonjour, TestUser !" }
   ```

** capture sur le test sur postman 

![image](https://github.com/user-attachments/assets/a38fc479-d30b-47fd-92ac-746a4033f4e6)
