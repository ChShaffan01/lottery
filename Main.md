// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

contract Lottery {
    address public Manager;
    uint public TicketAmount = 1 ether;

    constructor(){
        Manager = msg.sender;
    }

    address[] public Players;

    function AlreadyEntered() private view returns(bool){
        for (uint i = 0; i < Players.length; i++){
            if(msg.sender == Players[i]){
                return true;
            }
        }
        return false;
    }

    function Entry() public payable {
        require(msg.sender != Manager, "Manager Cannot Participate");
        require(msg.value >= TicketAmount, "Amount is less than Min Ticket Amount");
        require(AlreadyEntered() != true, "You've Already Participated");

        Players.push(msg.sender);
    }

    function PickWinner() public {
        require(msg.sender == Manager, "You Are Not Manager");
        require(Players.length >= 5, "Minimum Five Players Required");

        uint Prize = address(this).balance;

        uint Index = random();
        address payable winner = payable(Players[Index]);

        winner.transfer(Prize);
    }

    function random() private view returns(uint){
        bytes32 temp;
        temp = sha256(abi.encodePacked(block.number, block.timestamp, Players.length));
        uint Index = uint(temp);
        return Index % Players.length;
    }

    receive() external payable { }
}
