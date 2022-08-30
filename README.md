# ParchMint

Welcome to the ParchMint microfluidic benchmark repository. This is the current home of the ParchMint JSON validation schema, the set of known microfluidic benchmarks following that schema, and ongoing discussion about the evolution of the standard.

## Schema

We provide a JSON schema file that can be used to validate new ParchMint architecture files. The architecture files contain five top-level JSON keys: `layers`, `components`, `connections`, `features`, and `name`. The `name` key is simply a human readable name for your architecture. It is a required field, but is not required to be unique (although this is highly encouraged). The other top-level keys, and their members are described below.

### Layers

The `layers` key contains an array of layer ojects. Each individual layer object contains a unique id string `id` and a human readable name string `name`.

```json
"layers": [
    {
        "id": "unique-flow-layer-id-string",
        "name": "flow-layer"
    },
    {
        "id": "unique-control-layer-id-string",
        "name": "control-layer"
    }
]
```

### Components

The `components` key contains an array of component objects which represent all the unplaced components in your microfluidic device. Each individual component object contains a unique id string `id` and a human readable name string `name`. The component object also contains a number of keys which represent the components physical presence such as the `layers` key, which is an array of layer id's representing which layers this component exists on, the `x-span` and `y-span` integer keys which represent the the amount of space the component's bounding box takes in the x and y directions, respectively, and the `entity` string key which represents what type of component this component object represents. Finally the component object contains a `ports` key which contains an array of port objects. Each port object has a `label` string key which is similar to a unique id but is only required to be unique within that port list (this allows you to re-use the same port names between the same components), a `layer` string key which signifies which layer the port is located on (and should be one of the layers listed in the component object's layer list), and an `x` and `y` integer key which represents the ports x and y direction offset relative to the upper left of the component objects bounding box (making this port location relative to the component). Because this architecture system assumes zero knowledge of the component internals, ports should only exist at the edge of the component bounding box.

```json
"components": [
    {
        "id": "unique-mixer-id-string",
        "name": "mixer-001",
        "layers": [
            "unique-flow-layer-id-string",
            "unique-control-layer-id-string"
        ],
        "x-span": 4500,
        "y-span": 1500,
        "entity": "rotary-mixer",
        "ports": [
            {
                "label": "input-port",
                "layer": "unique-flow-layer-id-string",
                "x": 0,
                "y": 750
            },
            {
                "label": "output-port",
                "layer": "unique-flow-layer-id-string",
                "x": 4500,
                "y": 750
            },
            {
                "label": "rotary-control-port",
                "layer": "unique-control-layer-id-string",
                "x": 2250,
                "y": 0
            }
        ]
    }
]
```

### Connections

The `connectors` key contains an array of connection objects which represent all the unrouted connections in your microfluidic device. Each individual connection object contains a unique id string `id` and a human readable name string `name`. The connection object contains a `layer` key which is the layer id for the layer this connection exists on and a `source` and `sinks` key. The `source` and `sinks` keys each contain terminal objects, with the `source` containing a single terminal object and the `sinks` containing an array of terminal objects to allow for the representation of multinets. Each terminal object contains a `component` key which is a component id and represents which component this connecitons starts or ends at and an optional `port` key which represents which port on that component this connection uses if a specific port is required.

```json
"connections": [
    {
        "id": "unique-mixer-flow-connection-id",
        "name": "mixer-flow-connection",
        "layer": "unique-flow-layer-id-string",
        "source": {
            "component": "unique-mixer-id-string",
            "port": "input-port"
        },
        "sinks": [
            {
                "component": "unique-output-id-string",
                "port": "io-port"
            }
        ]
    }
]
```

### Features

The `features` key contains two different types of objects; component features which represent the placed information for a component for analysis and fabrication, and connection features which represent a straight line route segment of a connection.

#### Component Feature

The component feature contains `name`, `id`, and `layer` fields which should match the `name` and `id` field of the abstract component in the `components` array that it represents and one of the layers in the abstract components layers list. It should also contain a `location` object which contians `x` and `y` keys with the integer location of the component bounding boxes upper left point and a `x-span` and `y-span` keys which represent the size of the bounding box, and a `depth` key representing how deep the component should be.

```json
"features": [
    {
        "name": "mixer-001",
        "id": "unique-mixer-id-string",
        "layer": "unique-flow-layer-id-string",
        "location": {
            "x": 500,
            "y": 2000
        },
        "x-span": 4500,
        "y-span": 1500,
        "depth": 10
    }
]
```

#### Connection Feature

The connection feature contains an `id` field which should be a unique id string and a `name` field which should be a human readable name. It also contains a `connection` key which should be the id of the connection that this connection feature is a straight line segment of and a `layer` which should match the layer that that connection is supposed to be routed on unless this connection utilizes through-layer vias. It contains `width` and `depth` keys which represent the width of the channel tangentially to its direction of travel and its channel depth, respectively, as well as `source` and `sink` keys, which each contain a single object with `x` and `y` keys represent each end of this straight line segment. Finally, it contains a `type` key which currently is always set to `"channel"`.

```json
"features": [
    {
        "name": "mixer-flow-connection-segment-001",
        "id": "unique-channel-segment-id",
        "connection": "unique-mixer-flow-connection-id",
        "layer": "unique-flow-layer-id-string",
        "width": 5,
        "depth": 10,
        "source": {
            "x": 500,
            "y": 2750
        },
        "sink": {
            "x": 50,
            "y": 2750
        },
        "type": "channel"
    }
]
```

## Benchmarks

The `benchmarks/` directory currently holds the following benchmarks:

### applicaiton-converted

* `planar_synthetic_1.json`
* `planar_synthetic_2.json`
* `planar_synthetic_3.json`
* `planar_synthetic_4.json`
* `planar_synthetic_5.json`
* `planar_synthetic_6.json`
* `planar_synthetic_7.json`

### assay-inspired

* `aquaflex-3b.json`
* `aquaflex-5a.json`
* `chromatin_immunoprecipitation.json`
* `general_purpose_mfd.json`
* `hiv1_p24_immunoassay.json`
* `molecular_gradients_generator.json`


## Docker
Download and install Docker.

  + Linux: check your package manager / distribution instructions.
  + Mac: [Instructions](https://docs.docker.com/docker-for-mac/install/)
  + Windows: [Instructions](https://docs.docker.com/docker-for-windows/install/) (Windows 10) / [Instructions](https://docs.docker.com/toolbox/toolbox_install_windows/) (Windows 7/8)
  
Pull, Build and run the image:

    docker compose up --build
	
Navigate to <http://localhost:8080> to use.
### Docker Hub

  + Docker images are built and deployed automatically via Github Actions See `.github/workflows/deploy.yml`.
  + They are published via the [NonaSoftware Docker Hub](https://hub.docker.com/repository/docker/nonasoftware/).
## Tutorials

For more details on how to use Nona's supported software tools check out our [Youtube tutorials](https://www.youtube.com/watch?v=Xq2n-iSfmBE&list=PLJAqoPLx9EE3iKr35HgyxUIvHQcIJGK-7). These are still a work in progress, but we'll be updating them regularly.

## Mission, Values, and Roadmap

For more information about our plans for `Nona` you can read our [home page and DEI](https://nonasoftware.org), which includes more details on our vision for supporting an open-source ecosystem around synthetic biology.
You can see details of [Trello project roadmap here](https://trello.com/nonaresearchfoundation).

## Contributing

Contributions are encouraged! Please read our [contributing guide](./docs/CONTRIBUTING.md) to get started. Given that we're in an early stage, you may want to review our guides as well:

* [Bug reports](./docs/CONTRIBUTING.md#bugs)
* [Feature requests](./docs/CONTRIBUTING.md#features)
* [Pull requests](./docs/CONTRIBUTING.md#pull-requests)

## Code of Conduct

`Nona` has a [Code of Conduct](./docs/CODE_OF_CONDUCT.md) that should be honored by everyone who participates in the `Nona` community.

## Governance

You can learn more about how the `Nona Research Foundation` is organized and managed from our [governance model](), which includes information about, and ways to contact the [@nonasoftware/steering-council and @napari/core-devs]().

## help

We're a community over in [Slack for help](https://join.slack.com/t/nona-network/shared_invite/zt-1etjfumia-3w4JreBT1rw_fQcPLM7dDw) and all help and support requests in an appropriate `puppeteer-help` channel. We look forward to interacting with you there. 

Bug reports should be made on our [github issues](https://github.com/nonasoftware/puppeteer/issues/new?template=bug_report.md) using
the bug report template. If you think something isn't working, don't hesitate to reach out - it is probably us and not you!

## institutional and funding partners

![Nona Research Foundation](https://nonasofware.org) 
Want to be included as a partner or sponsor? Reach out to donate@nonasoftware.org