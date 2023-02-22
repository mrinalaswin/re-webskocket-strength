# Calculate the websocket strength
## Input
- ** Websocket Object ** : The websocket object of an initialized connection.
## Output
- ** Websocket Strength ** : The websocket strength in percentage.
## Interface
- findWebsocketStrength(ws: Websocket) => int
## Test cases
| Test case ID | Input | Expected Output |
|--------------|-------|-----------------|
|	AAA001 | A valid websocket object is provided | The system should prompt the strength percentage of the socket connection |
| AAA002 | A websocket object whose connection is closed or rejected | The system should prompt the error stating that the connection is closed |
| AAA003 | A websocket connection is closed in between | The system should prompt the error stating that the connection is closed |
| AAA004 | Client connection is interupted | The system should prompt that the user connection is interupted | 
| AAA005 | Client connection is interupted in between | The system should prompt that the user connection is interupted | 
| AAA006 | An unknown error happens | The system should catch the error and prompt the unknown error happened|
