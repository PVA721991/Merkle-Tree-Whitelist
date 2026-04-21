# Merkle-Tree-Whitelist
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract SimpleWhitelist {
    bytes32 public merkleRoot;
    mapping(address => bool) public hasClaimed;

    constructor(bytes32 _merkleRoot) {
        merkleRoot = _merkleRoot;
    }

    function claim(bytes32[] calldata proof) public {
        require(!hasClaimed[msg.sender], "Already claimed");
        bytes32 leaf = keccak256(abi.encodePacked(msg.sender));
        require(verify(proof, leaf), "Invalid proof");
        hasClaimed[msg.sender] = true;
        // Add your reward logic here (e.g., mint NFT or transfer tokens)
    }

    function verify(bytes32[] calldata proof, bytes32 leaf) internal pure returns (bool) {
        bytes32 computedHash = leaf;
        for (uint256 i = 0; i < proof.length; i++) {
            computedHash = _hashPair(computedHash, proof[i]);
        }
        return computedHash == merkleRoot;
    }

    function _hashPair(bytes32 a, bytes32 b) private pure returns (bytes32) {
        return a < b ? keccak256(abi.encodePacked(a, b)) : keccak256(abi.encodePacked(b, a));
    }
}
