# flowlink

FlowLink is a repository containing code to analyze various traffic flow records such as Netflow, Ipfix, Sflow, AWS Cloudwatch VPC Flow logs, textual data and send traffic flow information to the Illumio PCE traffic collector for display using its illumation feature.

# Source Code Overview
The code consists of the 5 components:

## Data Connectors
The purpose of a data consumer is to connect to the source of the data flow and accept data flow records and pass them onto the appropriate data flow parser. Consumers either listen on UDP or TCP ports for incoming packets or connect to a data source such as kafka or AWS Cloudwatch to retrive data packets and post the packets on the appropriate parser queue.

## Parsers
There is a parser for each type of data flow supported. Each parser dequeues packets from its queue and parses the associated data format and extracts and normalizes the data before sending it to the data aggregator.

## Data Aggregator
The aggregator will accept normalized data from the all the parsers and aggregate the flow data over a configured period of time before passing off the data to the uploader for sending to the PCE.

## Uploader
The uploader receives aggregated flow information from the aggregator and it transforms this data into a protobuf payload to be sent to the PCE. If data cannot be sent due the PCE being down or overloaded, data will be written to disk for sending at a later time.

## Configuration File
The configuration file drives the FlowLink application and it provides information such as:
  - Illumio PCE API credentails
  - Illumio PCE host address
  - Configuration for parsers and associated connectors

A full description of the configuration file can be found in the flowlink_config_schema.json file. An example of a configuration file can be found in the config_yaml.example file.

# Compiling FlowLink source code
1. Install go >= 1.12.6
2. cd go/src/flowlink
3. Set your GOPATH to include the current directory
4. go get && go generate && go build

# Running FlowLink
1. Create a confiuration file, see config.yaml.exmaple and flowlink_config_schema.json for information
2. CONFIG_FILE=path-to-config-file ./flowlink
