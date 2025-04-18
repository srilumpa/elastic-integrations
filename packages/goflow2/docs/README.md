# GoFlow2

The GoFlow2 integration allows you to import logs generated by goflow2.

The only protocol/normalisation of goflow2 that is supported in this integration is sFlow.
The normalisation of IPFIX and/or NetFlow is not yet support.

## Data streams
### sflow
The Goflow2 sFlow integration collects one type of data streams: logs

#### Sample Event
An example event for `sflow` looks as following:

```json
{
    "@timestamp": "2024-07-31T00:00:59.314Z",
    "destination": {
        "address": [
            "216.160.83.58"
        ],
        "as": {
            "number": 209
        },
        "geo": {
            "city_name": "Milton",
            "continent_name": "North America",
            "country_iso_code": "US",
            "country_name": "United States",
            "location": {
                "lat": 47.2513,
                "lon": -122.3149
            },
            "region_iso_code": "US-WA",
            "region_name": "Washington"
        },
        "ip": "216.160.83.58",
        "port": 443
    },
    "ecs": {
        "version": "8.17.0"
    },
    "event": {
        "action": "SFLOW_5",
        "category": [
            "network"
        ],
        "kind": "event",
        "original": "{\"type\":\"SFLOW_5\",\"time_flow_start_ns\":1722384059314899647,\"sampler_address\":\"67.43.156.1\",\"sequence_num\":44555,\"in_if\":563,\"out_if\":573,\"src_addr\":\"216.160.83.57\",\"dst_addr\":\"216.160.83.58\",\"etype\":\"IPv4\",\"proto\":\"TCP\",\"src_port\":10876,\"dst_port\":443,\"src_vlan\":1500,\"dst_vlan\":1500,\"sampling_rate\":1000,\"bytes\":70}",
        "timezone": "+00:00",
        "type": [
            "connection"
        ]
    },
    "network": {
        "bytes": 70000,
        "packets": 1000,
        "transport": "tcp",
        "type": "ipv4"
    },
    "observer": {
        "egress": {
            "interface": {
                "id": "573"
            },
            "vlan": {
                "id": "1500"
            }
        },
        "ingress": {
            "interface": {
                "id": "563"
            },
            "vlan": {
                "id": "1500"
            }
        },
        "ip": [
            "67.43.156.1"
        ]
    },
    "related": {
        "ip": [
            "216.160.83.57",
            "216.160.83.58"
        ]
    },
    "sflow": {
        "bytes": 70,
        "sample_rate": 1000,
        "sequence_num": 44555
    },
    "source": {
        "address": [
            "216.160.83.57"
        ],
        "as": {
            "number": 209
        },
        "geo": {
            "city_name": "Milton",
            "continent_name": "North America",
            "country_iso_code": "US",
            "country_name": "United States",
            "location": {
                "lat": 47.2513,
                "lon": -122.3149
            },
            "region_iso_code": "US-WA",
            "region_name": "Washington"
        },
        "ip": "216.160.83.57",
        "port": 10876
    },
    "tags": [
        "preserve_original_event",
        "forwarded",
        "sflow"
    ]
}
```

## Requirements

You need Elasticsearch for storing and searching your data and Kibana for visualizing and managing it.
You can use our hosted Elasticsearch Service on Elastic Cloud, which is recommended, or self-manage the Elastic Stack on your own hardware.

You need GoFlow2 to create log files for sFlow traffic.
https://github.com/netsampler/goflow2

## Setup

- Install integration and role out elastic agent
- Install GoFlow2 for sFlow logging

Please use the following GoFlow2 mapping.yaml file:

```
# File: /etc/goflow2/mapping.yaml
formatter:
    fields: # list of fields to format in JSON
        - type
        - time_flow_start_ns
        - sampler_address
        - sequence_num
        - in_if
        - out_if
        - src_addr
        - dst_addr
        - etype
        - proto
        - src_port
        - dst_port
        - src_vlan
        - dst_vlan
        - sampling_rate
        - bytes
```

The output sflow transport files must be stored in the directory ```/var/log/sflow/goflow2/```

Full command to run GoFlow2 for sflow traffic:
```shell
goflow2 -format json -listen "sflow://:6343" -mapping /etc/goflow2/mapping.yaml -transport.file /var/log/sflow/goflow2/goflow2.log
```

## Fields
**Exported fields**

| Field | Description | Type |
|---|---|---|
| @timestamp | Event timestamp. | date |
| data_stream.dataset | Data stream dataset. | constant_keyword |
| data_stream.namespace | Data stream namespace. | constant_keyword |
| data_stream.type | Data stream type. | constant_keyword |
| input.type | Input type | keyword |
| log.file.device_id | Device Id of the log file this event came from. | keyword |
| log.file.inode | Inode of the log file this event came from. | keyword |
| log.offset | Log offset | long |
| sflow.bytes | Original size in  bytes of the sample packet. | long |
| sflow.sample_rate | sample rate. | long |
| sflow.sequence_num | flow sequence number. | long |

