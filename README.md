# TCP Message Processor for Bodet Scorepad

This repository contains a Python script and additional files to process and analyze data transmitted over TCP from the [Bodet Scorepad](https://www.bodet-sport.com/products/sports-display-control/control-keyboard.html). 
The script is designed to parse specific messages and extract details such as scores, time, and game period. It also writes the current status into a JSON file for easy integration with other systems.

## Features

Currently the script *only listens for data related to floorball*. It serves as a template for other sports.   

- Captures real-time data from a TCP socket.
- Filters and processes only messages with a specific type identifier (number `11`).
- Message `11` contains the following data: 
  - Home team score.
  - Guest team score.
  - Current game time (minutes and seconds).
  - Current game period (including overtime indicated as "E").
- Outputs the parsed information to the console and a JSON file.

## Prerequisites

- Python 3.6 or higher

## Installation

1. Clone the repository:
```bash
   git clone https://github.com/christoph-ernst/bodet-scorepad-parser.git
   cd bodet-scorepad-parser
```
2. Install any necessary dependencies (if applicable). This script does not require external libraries apart from the standard Python library.
   
## Usage
### Running the Script
1. Start the script:
```bash
python bodet-network.py
```
3. The script will listen for incoming TCP connections on the specified port (default is `4001`).
4. make sure to configure your Bodet Scorepad accordingly. The [Guide from Bodet](https://static.bodet-sport.com/images/stories/EN/support/Pdfs/manuals/Scorepad/608264-Network%20output%20and%20protocols-Scorepad.pdf) explains how to achive that.

### Testing
If you happen not to have a Scorepad with you all the time you can make use of the `test.sh` script. 
IT will send some data to localhost:4001

## Output
- Console Output: Each parsed message will be displayed in the console, showing the home score, guest score, time, and period.
Console:
```yaml
Message with number 11 (1): Mins: 2, Secs: 15, Score Home: 0, Score Guest: 20, Period: 1
```` 
- JSON File: The current status is saved to `status.json` in the following format:
```json
{
    "score_home": 5,
    "score_guest": 4,
    "time": "12:34",
    "period": 3
}
```

## Configuration
- Host and Port:
Modify the `host` and `port` variables in the script to change where the server listens for incoming connections:
```python
host = '0.0.0.0'  # Listen on all interfaces
port = 4001       # Default port
```
- JSON File Location:
The JSON file location can be customized by changing the filename parameter in the `write_status_to_json` function.

# How It Works
1. The script listens for TCP connections and receives data in chunks.
2. Messages are parsed according to predefined markers and structure.
3. Only messages with type `11` are processed further.
   
   - message #11 means floorball. For other message types see the [Guide from Bodet](https://static.bodet-sport.com/images/stories/EN/support/Pdfs/manuals/Scorepad/608264-Network%20output%20and%20protocols-Scorepad.pdf)
  
5. Extracted information includes:
   
   - Scores (home and guest teams).
   - Time (in minutes and seconds).
   - Period (handles regular periods as integers and "E" for overtime).
    
5. The results are printed to the console and saved to a JSON file.

## Example Input

A typical message might look like this in hex:
```
0x01 0x00 0x02 0x31 0x31 0x30 0x32 0x31 0x35 0x30 0x30 0x30 0x32 0x30 0x30 0x31 0x03
```

- 0x01: Start of message (SOH)
- 0x02: Start of text (STX)
- 0x31 0x31: Message type 11
- 0x30 0x32: Minutes (02)
- 0x31 0x35: Seconds (15)
- 0x30 0x30 0x30: Home score (0)
- 0x30 0x32 0x30: Guest score (20)
- 0x31: Period (1)
- 0x03: End of text (ETX)

## what's next
- implement longitudinal redundancy check (LRC) check to check integrity of received messages
- implement additional message types
   -  Message #12: Home team players penalty
   -  Message #13: Guest team players penalty

## License
This project is licensed under the GNU GENERAL PUBLIC LICENSE Version 3. See the LICENSE file for details.

