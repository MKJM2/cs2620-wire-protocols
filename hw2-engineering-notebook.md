# HW2 

### Link to the Code
Our codebase can be found in the following [public GitHub repository](https://github.com/MKJM2/cs2620-wire-protocols):
```
https://github.com/MKJM2/cs2620-wire-protocols
```

---

## Does the Use of This Tool (gRPC) Make the Application Easier or More Difficult?
Definitely easier. Serialization/deserialization seemed to us like a very "automatable" task, so having something do most of the work for us, allowing us to focus on the actual interface, is great!

---

## Ease of Implementation & Extensibility
- **No need to write a custom parser**: Unlike our binary protocol, gRPC eliminates the need to write custom encoding/decoding logic.
  
- **JSON**: JSON is easy to extend—just add new fields. However, it lacks the efficiency of binary serialization.

- **Custom Wire Protocol**: Our custom wire protocol isn't necessarily hard to extend, but it’s definitely annoying. For every new `op_code` we want to support, we have to implement additional encoding/decoding logic.

- **Protobuf/gRPC**: Protobuf and gRPC are super easy to extend. You just modify the `.proto` definition and recompile the code! The decoding/encoding logic is automatically handled for you. It reminds me of using `serde` in Rust.

---

## What Does It Do to the Size of the Data Passed? Memory Usage Benchmarking

### Benchmarking Against JSON and Our Custom Protocols
A challenge with benchmarking is that many of the message types have variable or unbounded lengths. For example, a client sending a message can send a message of any arbitrary length.

After some deliberation, we decided on the following approach:

- For message types (`opcodes`) where the length isn't variable, we provide a detailed size comparison in the table below.
- For variable-length messages, we test across a range of realistic scenarios and edge cases to account for variability.

### Assumptions and Limitations
- **Insecure gRPC Channels**: For this analysis, we use insecure gRPC channels, so we do not account for overhead from TLS encryption or HTTP/2 headers. We only measure the size of the actual payload.
- **Layer Differences**: This analysis isn't fully fair because:
  - Operating on raw sockets (our custom protocol) works directly on **Layer 4** (Transport Layer) of the OSI model (e.g., TCP or UDP).
  - gRPC is an **application-layer protocol** (Layer 7), built on top of HTTP/2.
  - By design, gRPC provides additional features like multiplexing, flow control, and built-in support for streaming, which inherently add overhead.

Because the protocols operate at different levels of abstraction, we decided to focus on **raw serialization efficiency**. Specifically, we compare:
- The size of the JSON dump.
- The size of our custom binary payload.
- The size of the protobuf payload sent over the network.

---

### Size Comparison Table (TODO: Fill in the table with actual data)

| **Message Type**       | **JSON Size (bytes)** | **Custom Protocol Size (bytes)** | **Protobuf Size (bytes)** |
|-------------------------|-----------------------|-----------------------------------|---------------------------|
| LOGIN                  | TODO                 | TODO                             | TODO                     |
| CREATE_ACCOUNT         | TODO                 | TODO                             | TODO                     |
| LIST_ACCOUNTS (1 page) | TODO                 | TODO                             | TODO                     |
| SEND_MESSAGE           | TODO                 | TODO                             | TODO                     |
| READ_MESSAGES (1 page) | TODO                 | TODO                             | TODO                     |

---

### Testing Across Realistic Scenarios and Edge Cases

#### Realistic Scenarios
1. **Small Messages**:
   - Example: A simple "Hello" message.
   - Purpose: Highlights the overhead of headers/metadata for each protocol, which will comprise a large proportion of the bytes sent over the network.

2. **Medium Messages**:
   - Example: A typical chat message consisting of a few words or a single page of accounts for the `LIST_ACCOUNTS` operator.
   - Purpose: Represents the average case request sent in a chat application.

3. **Large Messages**:
   - Example: A list of hundreds of accounts or a large chat message consisting of multiple English sentences. Another example is fetching unread messages where there are 100+ unread messages.
   - Purpose: Tests how well each protocol handles large payloads and whether the overhead scales with payload size.

#### Edge Cases
1. **Empty Payloads**:
   - Example: Protocols that allow empty payloads. Sending an empty message to a user.
   - Purpose: Provides an estimate of the specific number of overhead bytes in each protocol.

2. **Maximum-Sized Payloads**:
   - Example: Messages with the largest allowed content for each protocol.
   - Purpose: Tests whether, in the limit, the size of the overhead becomes negligible compared to the size of the actual message contents. (TODO: Verify this experimentally!)

---

## How Does It Change the Structure of the Client? The Server?

(TODO)

---

## How Does This Change the Testing of the Application?

(TODO)
