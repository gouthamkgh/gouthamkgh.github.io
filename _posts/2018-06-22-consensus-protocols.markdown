---
layout: post
title:  Consensus Protocols - Raft
date:   2018-06-22 13:30:00
categories: main
---

## What is distributed consensus ?
----------------------------------

Consider a system with a single node (say, a database server). We have a client that sends a value to our server. Because it's a single node, coming to a consensus/agreement is trivial.  

But what if we have multiple nodes? That's the problem of _distributed consensus_. Raft is a protocol for implementing distributed consensus.

## High level overview
-----------------------

A node can be in one of three states:
1. Follower
2. Candidate
3. Leader

### Leader election overview:

1. All nodes start as followers
2. If followers don't hear from a leader, they can become a Candidate
3. Candidate requests votes from other nodes 
4. Other nodes vote - if majority nodes vote for the Candidate, it becomes a Leader
5. All changes to the system now go via the Leader

### Log replication overview:

1. Each change to the system goes via the Leader
2. Each change is added as an entry in the node's log (but is not committed) - so the node value is not updated
3. To commit the entry, the node first replicates it to all the Follower nodes
4. The Leader then waits until a majority of nodes have written the entry
5. The entry is now committed to the Leader node
6. The Leader then notifies the Follower nodes that tne entry is committed
7. The cluster has now come to consensus about the system state 

## Leader election in Raft:
---------------------------

There are two timeout settings in Raft that control elections:
1. _Election timeout_: 
   - Amount of time a Follower waits until becoming a Candidate. In Raft, it's randomized to be between 150 - 300 ms (different nodes have different timeouts)
   - Whichever Follower node finishes the Election timeout first becomes a Candidate and starts a new _election term_ => it votes for itself, and sends out _Request Vote_ messages to other nodes 
   - If the receiving node hasn't voted yet in this term, it votes for the candidate, and it resets its election timeout 
   - Because we require a majority of votes, only one leader can be guaranteed per term.

2. _Heartbeat timeout_:
   - The leader begins sending out Append Entries messages to its followers.
   - These messages are sent in intervals specified by the heartbeat timeout.
   - Followers then respond to each Append Entries message
   - This election term will continue until a follower stops receiving heartbeats and becomes a candidate.

Term => something akin to a session, where a certain node assumes the role of a Leader  

In case of split votes, the nodes wait for a new election and try again.  

## Log Replication in Raft:
---------------------------

- Once we have a leader elected we need to replicate all changes to our system to all nodes. This is done by using the same Append Entries message that was used for heartbeats.  
- First a client sends a change to the leader. 
- The change is appended to the leader's log, then the change is sent to the followers on the next heartbeat.
- An entry is committed once a majority of followers acknowledge it, and a response is sent to the client

## Consistency and Partition tolerance:
---------------------------------------

Raft can even stay consistent in the face of network partitions. If we have two partitions, we will end up having 2 leaders in two different terms.  

- Let's say we have 5 nodes A,B,C,D,E with A as the leader and there was a network partition
- Let's say Partition_1 has two nodes A and B with A as the Leader
- Partition_2 has three nodes C, D, E

Assume Client_1 writes to Partition_1  
- It sends a request to the current Leader - node A
- Node A sees that it can't replicate to majority, so the log entries don't get committed 
- Meanwhile, in Partition_2, a new Leader election happens (let's say C is the new leader)  

Assume Client_2 sends a write request to node C  
- Node C sends heartbeats, gets majority as usual, and then commits its entries  

Now let's heal the partition  
- Node A will see that Node C is the Leader in the latest term
- So node A will become a follower 
- Nodes A and B will revert their logs and sync up with the logs the new Leader (C) has and commit them

Thus we have consistency and partition tolerance.