# Components
## Base-xapp (`xapp-oai` submodule)
This is a basic xapp that subscribes to selected ran parameters, receives periodic indications messages and sends control requests to update these parameters.
The base xapp can communicate with the gNB by means of a RIC, or via direct socket communication. 

## xapp-sm-connector (`xapp-oai` submodule)
This components connects the xapp with the RIC. In downstream (from the xapp to the gnb), the connector receives our custom sm buffers to be encapsulated in E2AP messages and sent to the RIC. In upstream (from the gNB to the xapp), the connector retrieves custom sm buffers from E2AP to be sent to the xapp 

## RIC Components 
See https://github.com/wineslab/colosseum-near-rt-ric

## e2sim (`ocp-e2sim` submodule)
Similar to what the xapp-sm-connector does, e2sim encapsulate/de-encapsulate custom sm buffers that are to be sent or that are received from the gNB. It communicates with the gNB via UDP sockets. 

## gNB Emulator (`e2protobuf` submodule)
This is a simple gNB emulator to test our custom sm without running a real gNB. 

## Protobuf definitions (`oai-oran-protolib` submodule)
This repo contains the protobuf definitions of our custom 

## OpenAirInterface with xapp agent (`openairinterface5g` submodule)
This is a fork of OpenAirInterface with the addition of a custom xapp agent

# Running base xApp with gNB emulator 
Start by making a reservation that includes `oai-ric`, the login password is `pass`.  For the following steps, we recommend using a terminal multiplexer such as `tmux` or `screen`.

Start the gNB emulator:
```
cd
e2protobuf/build/gnb_e2server_emu
```
The emulated gNB is waiting for incoming messages.

Start the RIC:
```
cd
./start_ric_local_gnb.sh
```

and then that the following containers are running with `docker ps`:
```
e2term:ricindi
e2mgr:latest
e2rtmansim:latest
dbaas:latest
```

Start `e2sim`:
```
cd ocp-e2sim
./run_e2sim.sh
```
If in `e2mgr` logs you see something like this it means that `e2sim` has successfully registered with the RIC:
>```
>{"crit":"INFO","ts":1666880119183,"id":"E2Manager","msg":"#RmrSender.Send - RAN name: >gnb_734_733_b5c67780 , Message type: 12002 - Successfully sent RMR message","mdc":>>>>{"time":"2022-10-27 14:15:19.183"}}
>```

Start the base xapp container: 
```
cd
./start_xapp_container
```

Start `xapp-sm-connector`:
```
docker exec -it base-xapp:24 bash
cd ../xapp-oai/xapp-sm-connector/
./run_xapp.sh
```
Wait for the following lines to appear:
>```
>about to call xapp startup
>Still waiting for indreq buf...
>Opened control socket server on port 7000
>```
The connector is now ready to communicate with the base xapp, let’s run it:
```
docker exec -it base-xapp:24 bash
cd ../xapp-oai/base-xapp/
python3 run_xapp.py
```
The base xapp will now receive periodic RIC indication messages from the gNB. When an indication message is received, the xapp sends a control request to write the target RAN parameters with random data. 
This is how it normally looks like in the base xapp:
>```
>Received  14  bytes
>Recevied RIC indication response:
>param_map {
>  key: GNB_ID
>  value: "8"
>}
>param_map {
>  key: SOMETHING
>  value: "1"
>}
>
>Sending RIC indication control with random data
>printing built control message:
>msg_type: CONTROL
>ran_control_request {
>  target_param_map {
>    key: GNB_ID
>    value: "5"
>  }
>  target_param_map {
>    key: SOMETHING
>    value: "3"
>  }
>}
>
>Socket sent 18 bytes
>
>```
And this is how it looks like in the gNB:
>```
>Received 8 bytes
>ran message id 2
>Indication request message received
>Indication request for 2 parameters:
>        Parameter id 1 requested (a.k.a gnb_id)
>        Parameter id 2 requested (a.k.a something)
>Sending indication response
>Sent 14 bytes, buflen was 14
>
>-------------------------------
>Received 18 bytes
>ran message id 4
>Control message received
>Applying target parameter gnb_id with value 5
>Applying target parameter something with value 3
>
>-------------------------------
>```

# Running base xApp with OAI gNB 
This requires a running reservation with `oai-ric` and `eugenio-ran-dev`. The procedure is similar to the gNB emulator case, with the following differences:

Start the RIC in the SRN running with `oai-ric`:
```
start_ric.sh
```
Start the gNB in the SRN running with `eugenio-ran-dev`:
````
./run_gnb.sh -t donor
````
Then start `e2term` in the same SRN:
````
cd ocp-e2sim
./run_e2sim.sh
````
