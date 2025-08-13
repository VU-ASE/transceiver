import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# Overview

## Purpose 

The `transceiver` service is a special service, made for debugging other ASE services running in a pipeline. It enables two-way communication to all running services to provide tuning and debugging functionalities.

:::tip

We do not recommend installing this service manually, as the setup of the `transceiver` service is fairly complex. When you click "enable debug mode" in `roverctl-web`, this service will be downloaded and configured for you automatically.

In case you want to adjust this transceiver for your own needs, we advise to check out the "knowing more" series on the ASE website.

:::

## Installation

To install this service, the latest release of [`roverctl`](https://ase.vu.nl/docs/framework/Software/rover/roverctl/installation) should be installed for your system and your Rover should be powered on.

<Tabs groupId="installation-method">
<TabItem value="roverctl" label="Using roverctl" default>

1. Install the service from your terminal
```bash
# Replace ROVER_NUMBER with your the number label on your Rover (e.g. 7)
roverctl service install -r <ROVER_NUMBER> https://github.com/VU-ASE/transceiver/releases/latest/download/transceiver.zip 
```

</TabItem>
<TabItem value="roverctl-web" label="Using roverctl-web">

**Recommended**: click "enable debug mode" in the web interface.

OR:

1. Open `roverctl-web` for your Rover
```bash
# Replace ROVER_NUMBER with your the number label on your Rover (e.g. 7)
roverctl -r <ROVER_NUMBER>
```
2. Click on "install a service" button on the bottom left, and click "install from URL"
3. Enter the URL of the latest release:
```
https://github.com/VU-ASE/transceiver/releases/latest/download/transceiver.zip 
```

</TabItem>
</Tabs>

Follow [this tutorial](https://ase.vu.nl/docs/tutorials/write-a-service/upload) to understand how to use an ASE service. You can find more useful `roverctl` commands [here](/docs/framework/Software/rover/roverctl/usage)

## Requirements

- A `passthrough` webRTC service should be running, and its address should be configured in the [*service.yaml*](https://github.com/VU-ASE/transceiver/blob/aa3f09ec30f30c1c58c33f3b452d2439cdb56453/service.yaml#L17)
    - When you use `roverctl-web`, this will be configured for you automatically

## Inputs

As defined in the [*service.yaml*](https://github.com/VU-ASE/transceiver/blob/main/service.yaml), this service officially does not depend on any other services. Yet, when `roverd` detects a service that registers as `transceiver` it will pass *all* other services as inputs, so this `transceiver` service will listen to all other services for debugging purposes.

## Outputs

As defined in the [*service.yaml*](https://github.com/VU-ASE/transceiver/blob/main/service.yaml), this service exposes the following write streams:

- `transceiver`:
    - To this stream, [`TuningState`](https://github.com/VU-ASE/rovercom/blob/c1d6569558e26d323fecc17d01117dbd089609cc/definitions/tuning/tuning.proto#L12) messages will be written.
    - This stream is passed to all other services in the pipeline by `roverd` to enable two-way communication between the `transceiver` and another service.
