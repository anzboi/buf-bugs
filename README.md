# buf bugs

## Buf format deletes comments after some options

In some cases, `buf format` will delete comments on options. We detected this specifically after an empty repeated option on rpcs with a trailing `,`. A minimal example is in `module/format.proto`

```
buf format --path module/format.proto
```

**Expected output:**

```proto
syntax = 'proto3';

package module;

import "google/api/annotations.proto";

service Service {
  rpc Method(Message) returns (Message) {
    option (google.api.http) = {
      additional_bindings: [] /* This comment will get deleted */
      additional_bindings: [] /* This comment is safe */
      additional_bindings: [] /* this one is not */
      body: "*" // but this one is
    };
  }
}

message Message {}
```

**Actual output:**

```proto
syntax = 'proto3';

package module;

import "google/api/annotations.proto";

service Service {
  rpc Method(Message) returns (Message) {
    option (google.api.http) = {
      additional_bindings: []
      additional_bindings: [] /* This comment is safe */
      additional_bindings: []
      body: "*" // but this one is
    };
  }
}

message Message {}
```
