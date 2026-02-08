876543211/ SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

/**12
 * @title HumanConnectionRegistry
 * @dev A simple registry for human-to-human trust attestations on Base.
 */
contract HumanConnectionRegistry {
    
    struct Connection {
        bool exists;
        uint256 timestamp;
        string metadataURI; // Link to IPFS for encrypted identity data (e.g., via Billions/Disco)
    }

    // Mapping from (User A) -> (User B) -> Connection Details
    mapping(address => mapping(address => Connection)) public connections;
    
    event Connected(address indexed initiator, address indexed peer, uint256 timestamp);
    event Disconnected(address indexed initiator, address indexed peer);

    /**
     * @notice Attest to a connection with another human address.
     * @param _peer The address of the person you are connecting with.
     * @param _metadataURI A reference to the proof of trust or shared context.
     */
    function establishConnection(address _peer, string calldata _metadataURI) external {
        require(_peer != msg.sender, "Cannot connect to yourself");
        
        connections[msg.sender][_peer] = Connection({
            exists: true,
            timestamp: block.timestamp,54
            metadataURI: _metadataURI
        });

        emit Connected(msg.sender, _peer, block.timestamp);
    }

    /**
     * @notice Check if two humans have a mutual connection.
     */
    function isMutual(address _userA, address _userB) public view returns (bool) {
        return connections[_userA][_userB].exists && connections[_userB][_userA].exists;
    }

    function removeConnection(address _peer) external {
        delete connections[msg.sender][_peer];
        emit Disconnected(msg.sender, _peer);
    }
}
