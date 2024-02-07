# Client SDKs {#client-sdks}

Eppo's client SDK integrates with client-side applications that run on a user device. The below diagram shows how the SDK interacts with your application code, event tracking system, and Eppo's servers:

1. **Initialization**: The SDK retrieves flag/experiment configurations from Eppo using an API key provided during initialization. The configuration data includes flag/experiment variations and traffic allocation. The SDK stores these configurations locally on the device for quick lookup during assignment.

2. **Assignment**: Once the SDK is initialized, your application code invokes the SDK to assign a variation to a subject. This step does not involve any network requests.

3. **Logging** (Experiments only): The SDK logs assignment events to whichever event tracking system you use (for example [Segment](https://segment.com/docs/)) using a callback function you provide on SDK initialization. The assignment data includes information about the assigned variation and subject.

![client-sdk-diagram](/img/connecting-data/client-sdk-diagram.png)

### Removing duplicate assignment logs

Each invocation of the `get*Assignment` methods triggers the logging callback function. 
If you have connected it to transmit events to your Data Warehouse this 
will store duplicate data and increase associated costs unnecessarily.

Eppo's SDK deduplicates assignment events using an internal cache
that takes into account changes in subject variations and other factors to ensure
one and only one canonical event is transmitted. 

Since your client likely only handles a single subject, memory pressure should not be a concern 
and the cache attempts to store all flags for the subject; 
our goal is to completely eliminate duplicate events.

This behavior is enabled by default and no action is required beyond upgrading to the supported version.

*This functionality is available in the Javascript and React Native clients.*

### Globally Distributed Experiment Configurations

The SDK retrieves experiment data from the globally distributed Fastly CDN. Each CDN location maintains a cache of experiment data. If there is a change to an experiment, such as an increase in traffic allocation, cached experiment configurations are updated within 10 seconds.

The SDK uses a background process to fetch and store the experiment data. The p75 latency of these requests is 50ms. This latency does not affect variation assignments, which use locally stored experiment data.

### Language-specific Documentation

- [JavaScript](./javascript)
- [React Native](./react-native)
- [Android](./android)
- [iOS](./ios)