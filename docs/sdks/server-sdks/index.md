# Server SDKs {#server-sdks}

Eppo's server-side SDKs may be used to implement flags and run experiments in your application server code. The below diagram shows the interactions of the SDK with Eppo's server:

1. **Initialization**: The SDK is designed to be initialized once when your application server starts up. Upon initialization, the SDK begins polling Eppo's API at regular intervals (not configurable) to retrieve flag/experiment configurations. The flag/experiment configurations such as variations and traffic allocation are stored in memory for quick lookup during assignment.

2. **Assignment**: The application server code invokes the SDK to assign a subject (e.g. user ID) to an experiment variation.

3. **Logging** (Experiments only): The SDK logs assignment events to whichever event tracking system you use (for example [Segment](https://segment.com/docs/)) using a callback function you provide on SDK initialization. The assignment data includes information about the assigned variation and subject.

![server-sdk-diagram](/img/connecting-data/server-sdk-diagram.png)

### Reducing duplicate assignment logs

Each invocation of the `get*Assignment` methods may trigger the logging callback function. 
If you have connected it to transmit events to your Data Warehouse this 
will store duplicate data and increase associated costs unnecessarily.

Eppo's SDK deduplicates assignment events using an internal cache
that takes into account changes in subject variations and other factors to ensure
one and only one canonical event is transmitted; ideally on the order of a single event per 
subject assignment.

Each of your servers with Eppo's SDK will likely see assignment events from many subjects.
To completely eliminate duplicates would require a prohibitively large amount of memory,
therefore our goal is to dramatically reduce the number of duplicate events. 

To balance out memory usage with deduplication the internal cache is limited to 
50,000 keys which we estimate to allocate around 10 MB and it expires keys in least recently used order.

This behavior is enabled by default and no action is required beyond upgrading to the supported version.

*This functionality is available in the NodeJS client.*

### Language-specific Documentation

- [Node](./node)
- [Python](./python)
- [Java](./java)
- [Dot Net](./dotnet)
- [Go](./go)
- [Ruby](./ruby)
- [PHP](./php)