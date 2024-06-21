# Functions and Errors

This program contains a Solidity smart contract for managing room bookings on the blockchain. It employs a structured approach to room management, ensuring that only authorized users can add rooms, book them with correct payments, and cancel bookings to receive refunds. Error handling mechanisms are robustly implemented to maintain contract integrity and prevent unauthorized operations

## Getting Started

### Executing Program
1. Open Remix IDE or your preferred development environment.
2. Copy the Functions and Errors contract code into a new Solidity file (e.g., RoomBooking.sol).
3. Compile the contract using Solidity version 0.8.26.
4. Deploy the contract to a local blockchain or any Ethereum testnet/mainnet.


```bash
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

contract RoomBooking {
    // Define a structure to hold room details
    struct Room {
        uint256 rent;
        bool isBooked;
    }
    
    // Mapping to store rooms
    mapping(uint256 => Room) public rooms;

    // Owner of the contract
    address public owner;

    // Modifier to check if the sender is the owner
    modifier onlyOwner() {
        require(msg.sender == owner, "Only the owner can perform this action");
        _;
    }

    // Constructor to set the owner of the contract
    constructor() {
        owner = msg.sender;
    }

    // Function to add a new room
    function addRoom(uint256 roomId, uint256 price) external onlyOwner {
        // Check if the room already exists
        require(rooms[roomId].rent == 0, "Room already exists");

        // Add the new room
        rooms[roomId] = Room(price, false);
    }

    // Function to book a room
    function bookRoom(uint256 roomId) external payable {
        // Check if the room exists
        require(rooms[roomId].rent > 0, "Room does not exist");

        // Check if the room is already booked
        require(!rooms[roomId].isBooked, "Room is already booked");

        // Check if the correct amount is paid
        require(msg.value == rooms[roomId].rent, "Incorrect payment amount");

        // Book the room
        rooms[roomId].isBooked = true;

        // Assert to ensure payment transfer is successful
        assert(address(this).balance >= rooms[roomId].rent);
    }

    // Function to cancel a booking and refund the payment
    function cancelBooking(uint256 roomId) external {
        // Check if the room is booked
        require(rooms[roomId].isBooked, "Room is not booked");

        // Mark the room as not booked
        rooms[roomId].isBooked = false;

        // Refund the payment to the user
        payable(msg.sender).transfer(rooms[roomId].rent);
    }
}

```
## Authors

Anupam Kumar
- [@AnupamNeon](https://www.github.com/AnupamNeon)


## License

This project is licensed under the MIT License - see the LICENSE file for details.

