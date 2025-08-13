# Processing

This service follows the following steps:

:::info

The steps described here are highly simplified. To get a full understanding of this service, take a look at the "knowing more" series on the ASE website.

:::

1. It opens a webRTC connection to the `passthrough` server as defined in its [*service.yaml*](https://github.com/VU-ASE/transceiver/blob/aa3f09ec30f30c1c58c33f3b452d2439cdb56453/service.yaml#L17)
2. It opens read streams from all available services, as passed by `roverd`. For each service, it creates a new ZMQ subscriber socket
3. In the main loop, it iterates over all the opened subscriber sockets in a non-blocking fashion (using ZMQ [`DONTWAIT`](https://github.com/VU-ASE/transceiver/blob/aa3f09ec30f30c1c58c33f3b452d2439cdb56453/src/stream/stream.go#L130)). 
    - If there is data available, it will be wrapped in a [`DebugOutput` message](https://github.com/VU-ASE/rovercom/blob/c1d6569558e26d323fecc17d01117dbd089609cc/definitions/debug/debug.proto#L23) which is tagged with the metadata of the service that produced the original data. This message is then serialized and split up into one or more [segmentation messages](https://github.com/VU-ASE/transceiver/blob/main/src/segmentation/buffer.go) to stay within the maximum UDP packet size. Then it will be sent to the `passthrough` server.
4. If it receives a [`TuningState` message](https://github.com/VU-ASE/rovercom/blob/c1d6569558e26d323fecc17d01117dbd089609cc/definitions/tuning/tuning.proto#L12) from the `passthrough` server, it will write this message to its `transceiver` stream, as defined in its [*service.yaml*](https://github.com/VU-ASE/transceiver/blob/aa3f09ec30f30c1c58c33f3b452d2439cdb56453/service.yaml#L14)

