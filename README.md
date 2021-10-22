# hm-pktfwd
Helium Miner Packet Forwarder

This is a Python app that uses prebuilt utilities to detect the correct concentrator chip and region, then start the concentrator accordingly.

hm-pktfwd builds off three other repos which each built a portion of the code required to run the packet forwarder.

- [lora_gateway](https://github.com/NebraLtd/lora_gateway)
- [packet_forwarder](https://github.com/NebraLtd/packet_forwarder)
- [sx1302_hal](https://github.com/NebraLtd/sx1302_hal)

## reset_lgw.sh
`reset_lgw.sh` is a shared tool that is used on all concentrator chip versions.
On sx1301 chips, [its is recommended](https://github.com/NebraLtd/lora_gateway#31-reset_lgwsh) that the script is run before each time the concentrator is started.
On chips that use sx1302_hal, the reset script is [run automatically](https://github.com/NebraLtd/sx1302_hal/blob/3d73e6af43535f700ff7b6c2b49cc79d388cd70f/packet_forwarder/src/lora_pkt_fwd.c#L1656-L1662) when the concentrator starts and is expected to be located in the same directory as the `lora_pkt_fwd` module.

reset_lgw is used by all concentrators, and inspired by the [upstream](https://github.com/NebraLtd/lora_gateway/blob/971c52e3e0f953102c0b057c9fff9b1df8a84d66/reset_lgw.sh)
[versions](https://github.com/NebraLtd/sx1302_hal/blob/6324b7a568ee24dbd9c4da64df69169a22615311/tools/reset_lgw.sh).
That said, it is different from the originals, context specific to hm-pktfwd, and moved to this repo to avoid confusion about its intention.
Additional context [here](https://github.com/NebraLtd/sx1302_hal/pull/1#discussion_r733253225).

## Supported Region Plans

You can typically find the exact region plan you need to use at [What Helium Region](https://whatheliumregion.xyz/) or on the [Helium Miner GitHub repo](https://github.com/helium/miner/blob/master/priv/countries_reg_domains.csv) however the table below provides a rough guide...

| Region Plan | Region |
| --- | --- |
| AS923_1 | Most of Asia |
| AS923_2 | Vietnam and Indonesia |
| AS923_3 | Phillipines and Cuba |
| AS923_4 | Israel |
| AU915 | Australia, New Zealand and South America|
| CN470 | China |
| EU868 | Europe, Middle East and some of Africa |
| EU433 | Parts of Africa and Asia|
| IN865 | India and Pakistan |
| KR920 | South Korea |
| RU864 | Russia |
| US915 | North America |

Please note:
| Region Plan | Region |
| --- | --- |
| CN779 | NOT YET SUPPORTED |

## Building

### Pre built containers

This repo automatically builds docker containers and uploads them to two repositories for easy access:
- [hm-pktfwd on DockerHub](https://hub.docker.com/r/nebraltd/hm-pktfwd)
- [hm-pktfwd on GitHub Packages](https://github.com/NebraLtd/hm-pktfwd/pkgs/container/hm-pktfwd)

The images are tagged using the docker long and short commit SHAs for that release. The current version deployed to miners can be found in the [helium-miner-software repo](https://github.com/NebraLtd/helium-miner-software/blob/production/docker-compose.yml).

### Manual build

When developing, it is faster to build locally instead of relying on the pre-built container to generate.

```bash
# Cross-compile
docker buildx build --platform linux/arm64/v8 --progress=plain -t DOCKERHUB_USER/hm-pktfwd .

# To stop at an intermediary stage
docker buildx build --platform linux/arm64/v8 --progress=plain --target pktfwd-builder -t pktfwd-builder .

# Tag and push image
docker image tag docker.io/DOCKERHUB_USER/hm-pktfwd DOCKERHUB_USER/hm-pktfwd:0.0.X
docker push DOCKERHUB_USER/hm-pktfwd:0.0.X
```