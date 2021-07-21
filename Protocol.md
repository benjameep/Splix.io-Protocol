# Splix.io protocol

All messages start with this header

| Byte  | Data type | Description
|:------|-----------|------------
| 0     | uint8     | Packet name

## Servers
Servers are located by json file http://splix.io/json/servers.json

| Packet name | Name in game              | Description
|:-----------:|---------------------------|------------
| 1           | UPDATE_BLOCKS             | Not used? Didn't really see in actual game data. Maybe it's used in teams? All my data is for the regular mode. 
| 2           | PLAYER_POS                | Contains the position of any player within view
| 3           | FILL_AREA                 |
| 4           | SET_TRAIL                 |
| 5           | PLAYER_DIE                | Contains information on when a player within view dies
| 6           | CHUNK_OF_BLOCKS           |
| 7           | REMOVE_PLAYER             | Remove a player from view
| 8           | PLAYER_NAME               | Contains name of a specified player
| 9           | MY_SCORE                  | Contains your own score
| 10          | MY_RANK                   | Contains your own rank
| 11          | LEADERBOARD               | Contains every single leaderboard value
| 12          | MAP_SIZE                  | Defines the width and height of the map
| 13          | YOU_DED                   | Sent when you died
| 14          | MINIMAP                   | Contains every single minimap block to be rendered
| 15          | PLAYER_SKIN               | Contains skin of a specific player
| 16          | EMPTY_TRAIL_WITH_LAST_POS |
| 17          | READY                     | Sent when the server validates player spawn
| 18          | PLAYER_HIT_LINE           |
| 19          | REFRESH_AFTER_DIE         |
| 20          | PLAYER_HONK               | Sent when a player within view honks
| 21          | PONG                      | Sent when a player ping the server
| 22          | UNDO_PLAYER_DIE           | 
| 23          | TEAM_LIFE_COUNT           | 

### Packet "1" UPDATE_BLOCKS
Seems to update a single block with an id, despite the plural name

|Bytes | Data type | Description
|:-----|-----------|-------------------
| 1-2  | uint16    | X coord of block
| 3-4  | uint16    | Y coord of block
| 5    | uint8     | Block id


### Packet "2" PLAYER_POS

Contains the position of any player within view

| Bytes | Data type | Description
|:------|-----------|------------
| 1-2   | uint16    | The X position of the player to update
| 3-4   | uint16    | The Y position of the player to update
| 5-6   | uint16    | The ID of the player to update (ID is 0 = you)
| 7     | uint8     | The direction the player is headed (0-4)
| 8     | uint8     | (OPTIONAL) Add postion to player's trail (boolean)

### Packet "3" FILL_AREA

Fills a specified area with a single block type. Used for players boxing off areas.

| Bytes | Data type | Description
|:------|-----------|------------
| 1-2   | uint16    | X Position
| 3-4   | uint16    | Y Position
| 5-6   | uint16    | Width
| 7-8   | uint16    | Height
| 9     | uint8     | Color
| 10    | uint8     | Pattern

### Packet "4" SET_TRAIL

Sets the trail path of a player

| Bytes | Data type | Description
|:------|-----------|------------
| 1-2   | uint16    | Player ID
| 3-?   |           | (OPTIONAL) Trail

For each position in Trail:

| Bytes         | Data type | Description
|:--------------|-----------|------------
| ( i ) - (i+1) | uint16    | X Position
| (i+2) - (i+3) | uint16    | Y Position


### Packet "5" PLAYER_DIE
Sent when a player in view dies, i guess. Bytes 3-6 aren't always sent for some reason, and it seems to move the player???? You would think that's for the teams mode but the message occured in regular gameplay?? (at least I think, the game data is 2 years old, I don't remember what I did for it, see realdata/splixdata and realdata/splixdata2 in splixio-customserver/splixio-utilities)

|Bytes | Data type | Description
|:-----|-----------|-------------------
| 1-2  | uint16    | Player id of who died
| 3-4  | uint16    | Moves player to this X coord i think
| 5-6  | uint16    | Moves player to this Y coord i think


### Packet "6" CHUNK_OF_BLOCKS
Used for sending massive chunks of blocks to the client. It sends the block id of every block in the specified range. 

|Bytes | Data type | Description
|:-----|-----------|-------------------
| 1-2  | uint16    | Start X parameter
| 3-4  | uint16    | Start Y parameter
| 5-6  | uint16    | End X parameter
| 7-8  | uint16    | End Y parameter

Then this loops for every block in the range you specified (approx (endX-startX)*(endY-startY))

|Bytes | Data type | Description
|:-----|-----------|-------------------
| 9-?  | uint8     | Block id of block

### Packet "7" REMOVE_PLAYER

Remove a player from view

| Bytes | Data type | Description
|:------|-----------|------------
| 1-2   | uint16     | ID of player to remove (ID is 0 = you)

### Packet "8" PLAYER_NAME

Contains name of a specified player

| Bytes            | Data type | Description
|:-----------------|-----------|------------
| 1-2              | uint16    | ID of player to set name (ID is 0 = you)
| 3 - packet end   | uint8     | Username of the player

### Packet "9" MY_SCORE

Contains your own score

| Bytes | Data type | Description
|:------|-----------|------------
| 1-4   | uint32     | Your score as an integer
| 5-6   | uint16     | (OPTIONAL) Your kills as integer

### Packet "10" MY_RANK

Contains your own rank

| Bytes | Data type | Description
|:------|-----------|------------
| 1-2   | uint16     | Your rank as an integer

### Packet "11" LEADERBOARD

Contains every single leaderboard value

| Bytes | Data type | Description
|:------|-----------|------------
| 1-2   | uint16     | Total players in the server

For every player:

| Bytes                                 | Data type | Description
|:--------------------------------------|-----------|--------------------------------
| (index to index + 4)                  | uint32    | The player's score
| (index + 5)                           | uint8     | The length of the player's name
| (index + 6 to end of username length) | uint8     | The player's name

### Packet "12" MAP_SIZE

Defines the width and height of the map

| Bytes | Data type | Description
|:------|-----------|------------
| 1-2   | uint16     | Size of the map in integer (Server default is 600)

### Packet "13" YOU_DED

Sent when you died

Header only sometimes, but also can contain information on death:

| Bytes | Data type | Description
|:------|-----------|------------
| 1-4   | uint32     | The amount of blocks you owned upon death
| 5-6   | uint16     | The amount of kills you had upon death
| 7-8   | uint16     | Your rank you had upon death
| 9-12  | uint32     | The amount of time you were alive (in seconds)
| 13-16 | uint32     | (ONLY sent if you were number 1) The amount of time you were number 1 (in seconds)
| 17    | uint8     | The death type of how you died
| 18-end| uint8     | (ONLY if you died by a player) The name of the person who killed you

### Packet "14" MINIMAP
Contains the minimap. Essentially a simple monochrome bitmap, can't remember whether 0,0 is in bottom left or top left,
though. Each bit tells you whether it's white or black, so each bit corrresponds to a pixel on the minimap. 

|Bytes | Data type | Description
|:-----|-----------|-------------------
| 1    | uint8     | X start offset probably. Multipled by 20. 
| 2-?  | uint8?    | Each bit in each byte corresponds to a pixel in the minimap


### Packet "15" PLAYER_SKIN
Contains skin of a specific player

| Bytes | Data type | Description
|:------|-----------|----------------------------------------------------------------------------------------
| 1-2   | uint16     | Integer of the player ID (ID is 0 = you)
| 3     | uint8     | Player color
| 4     | uint8     | Player block pattern

### Packet "16" EMPTY_TRAIL_WITH_LAST_POS

### Packet "17" READY
Sent when the server validates player spawn.
Sent only packet header.

### Packet "18" PLAYER_HIT_LINE

### Packet "19" REFRESH_AFTER_DIE

### Packet "20" PLAYER_HONK
Sent when a player within view honks

| Bytes | Data type | Description
|:------|-----------|-------------------------------------------------------------------
|   1   | uint8     | Honk size (value 0-255, any bigger and the server will drop packet)

### Packet "21" PONG
Sent when a player pings the server.
Sent only packet header.

### Packet "22" UNDO_PLAYER_DIE

### Packet "23" TEAM_LIFE_COUNT

## Client

All messages are send as Uint8Array.

| Packet name | Name in game      | Description
|:-----------:|-------------------| ------------
| 1           | UPDATE_DIR        | Update direction
| 2           | SET_USERNAME      | Set username
| 3           | SKIN              | Set skin
| 4           | READY             | Sent after username and skin packet are send to initiate player spawn
| 5           | REQUEST_CLOSE     | Unused in game code
| 6           | HONK              | Send honk (honk is when you press space / hold down for a bigger honk size)
| 7           | PING              | Ping server
| 8           | REQUEST_MY_TRAIL  | Request for your trail (the path drawn when not in native territory)
| 9           | MY_TEAM_URL       |
| 10          | SET_TEAM_USERNAME | 
| 11          | VERSION           |
| 12          | PATREON_CODE      |

### Packet "1" UPDATE_DIR

Update direction

| Bytes | Data type | Description
|:------|-----------|------------
| 1     | uint8     | Direction (0-3)
| 2-3   | uint16     | X Coordinate
| 4-5   | uint16     | Y Coordinate

### Packet "2" SET_USERNAME

Set username

| Bytes | Data type | Description
|:------|-----------|------------
| 1-?   | string    | Send as decimal(ascii) value

### Packet "3" SKIN

Set skin

| Bytes | Data type | Description
|:------|-----------|------------
| 1     | uint8     | Block color
| 2     | uint8     | Block pattern

### Packet "4" READY

Sent after username and skin packet are send to initiate player spawn.
Sent only packet header.

### Packet "5" REQUEST_CLOSE

Unused in game code, you can ignore this packet.

### Packet "6" HONK

Send honk (honk is when you press space / hold down for a bigger honk size)

| Bytes | Data type | Description
|:------|-----------|------------
| 1     | uint8     | 0-1000ms normalized to 0-255

### Packet "7" PING

Ping server. Sent only packet header.

### Packet "8" REQUEST_MY_TRAIL

Request for your trail (the path drawn when not in native territory). Sent only packet header.

### Packet "9" MY_TEAM_URL

### Packet "10" SET_TEAM_USERNAME

### Packet "11" VERSION

### Packet "12" PATREON_CODE

## Connection
