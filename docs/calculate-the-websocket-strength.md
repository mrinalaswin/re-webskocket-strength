# Calculate the websocket strength
It is required to calculate the strength of the websocket, such that it can be used to show as a visualization as well as it can be used by the client to make decisions based on it.

```mermaid
sequenceDiagram
		Server ->> Client: {server_ts, data}
	activate Client
		Client -->> Server: {server_ts, client_ts}
	deactivate Client
		Server ->> Client: {server_ts, client_ts, server_ack_ts}
		activate Client
		Client ->> Client: {server_ts, client_ts, server_ack_ts, client_ack_ts}
		Client ->> Client: Calculate Latency
	deactivate Client
```

```mermaid
flowchart TD
	Start --> RST(Recieves server_timestamp)
	RST --> SCT(Sends client_timestamp)
	SCT --> RSAT(Recieves server_acknowledge_timestamp) 
	RSAT --> GCAT(Generates client_acknowledge_timestamp)
	GCAT --> CL("Calcualte Latency L(M) (server_ack_ts - server_ts) - ((client_ack_ts - client_ts) * 0.5")
	CL --> GT90{"if L(M) > 90"}
	GT90 -->|yes| US("Strength = Unstable")
	GT90 --> |no| GT80{"if L(M) > 80"}
	GT80 --> |yes| NG("Strength = Not Good")
	GT80 --> |no| GT70{"if L(M) > 70"}
	GT70 --> |yes| OK("Strength = Okay")
	GT70 --> |no| GT67{"if L(M)> 67"}
	GT67 --> |yes| VG("Strength = Very Good")
	GT67 --> |no| EX("Strength = Excellent")
	US --> Stop
	NG --> Stop
	OK --> Stop
	VG --> Stop
	EX --> Stop
```

```mermaid
stateDiagram
	[*] --> SET_SERVER_TS : "add_server_ts()"
	SET_SERVER_TS --> SET_CLIENT_TS : "generate_client_ts()"
	SET_CLIENT_TS --> SET_SERVER_ACK_TS : "add_server_ack_ts()"
	SET_SERVER_ACK_TS --> SET_CLIENT_ACK_TS : "generate_client_ts()"
	SET_CLIENT_ACK_TS --> [*] : "calculate_strength()"
	SET_CLIENT_TS --> [*] : "reset()"
	SET_SERVER_ACK_TS --> [*] : "reset()"
	SET_CLIENT_ACK_TS --> [*] : "reset()"
```

## Input
### server_ts
Timestamp when original message M was sent from the server
### client_ts 
Timestamp when original message M was recieved by the client
### server_ack_ts 
Timestamp when ACK message was recieved by the server.
### client_ack_ts
Timestamp when ACK response was recieved by the client

## Output
###  Strength 
The strength of the websocket connection with values either of :
- Unstable
- Not Good
- Okay
- Very Good
- Excellent
## Interface
### WsStrengthState 
```
type WsStrengthState = SET_CLIENT_TS | SET_SERVER_TS | SET_SERVER_ACK_TS | SET_CLIENT_ACK_TS
```
### WsStrengthAction = ADD_SERVER_TS | GENERATE_CLIENT_TS | ADD_SERVER_ACK_TS | GENERATE_CLIENT_ACK_TS | CALCULATE_STRENGTH | RESET
```
type WsStrengthAction = 
```
### WsStrengthValue
```
type WsStrengthState = UNSTABLE | NOT_GOOD | OKAY | VERY_GOOD | EXCELLENT
```
### WsStrength
```
type WsStrengthProp = {
	server_ts: int
	client_ts: option(int)
	server_ack_ts: option(int)
	client_ack_ts: option(int)
	strength: option(WsStrengthState)
	state : WsStrengthState
}
```
### setServerTs(serverTs: int) => WsStrengthProp
Sets the server timestamp to the `WsStrengthProp`
### generateClientTs() => WsStrengthProp
Generates the client timestamp when the server responds with its timestamp.
### setServerAckTs(serverAckTs: int) => WsStrengthProp
Add the server acknowledge timestamp to the WsStrengthProp
### generateClientAckTs() => WsStrengthProp
Generates the client timestamp when the server responds with its acknowledgement timestamp.
### calculateStrength(WsStrengthProp) => WsStrengthProp
Calculate the Strength of the Websocket connection
### reset() => WsStrengthProp
Resets the value of the `WsStrengthProp`
## Test cases
| Test case ID | Input | Expected Output |
|--------------|-------|-----------------|
|	AAA001 | The strength is calculated without recieving any of the `server_ts`, `client_ts`, `server_ack_ts` or `client_ack_ts` | The system should prompt saying the corresponding value need to be set |

## Acknowledgements
- https://www.securedgenetworks.com/blog/wifi-signal-strength
- https://ankitbko.github.io/blog/2022/06/websocket-latency/#:~:text=If%20the%20original%20message%20is,2%20gives%20one%20way%20latency.
