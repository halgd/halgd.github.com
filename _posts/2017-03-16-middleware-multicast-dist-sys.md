---
layout: post
title: "multicast in distributed systems"
description: "Middleware multicast methods and implementation in distributed systems"
keywords: "concept"
categories: systems
---

### FIFO multicast

Basically says that messsages from the same sender are delivered at each receiver in the order in which it was sent from that particular sender, we do not care in what relative order messages from different senders are delivered. Hence, the algorithm

*Notation*: the vector `pi[,,,]` denotes the number of events seen so far for each respective index (length of vector equals number of processes in the multicast group)

- When process pj sent a message 
  - pj[j] += 1
  - include pj[j] in the multicast message as its sequence number

- When process pi receives a mulicast from pj with sequence number S in message
```python
	if msg.ts == pi[j] + 1:
		deliver msg to application
		pi[j] += 1
	else:
		buffer msg till condition is true
```


### Totally ordered multicast 

Ensures that all receivers get the messages in the same order i.e. its focus is on the **consistency** of messages received across all processes in the multicast group. It doesn't guarentee messages of one sender are received in the same order it was sent. 

We use a central sequencer, so first we run a leader election algorithm to choose a sequencer or leader. Then the algorithm,

- When a process Pi wants to send a message
  - Sends the multicast to the group as well as the sequencer/leader
  - Sequencer maintains a global sequence number `S` initially 0
  - When it receives a message `M`, it sets `S = S + 1` and multicasts `<M,S>`

- Receive multicast at Pi
  - It maintains a local received global sequence number `Si` (initially 0)
  - If Pi receives a multicast M from Pj, it buffers it untill
   - Pi receives the multicast `<M, S(M)>` from the sequencer
   - `Si + 1 == S(M)`
  - After delivering the message to the application set `Si += 1`

### Causal ordering

Multicasts whose send events are causally related by a causal path, must be received in the same causality-obeying order at **all receivers**. Formally,
 - If send_event_multicast(g,m) -> send_event_mutlicast(g, m') then any correct process that delivers m' would already have been delivered m.
 - Each process Pi maintains a vector Pi[1..N] whose length equals the number of processes in the multicast group
 - Each value of the vector indicates the number of events that Pi has seen occured

When a process Pi wants to send a message
 - Sets Pi[i] += 1
 - Include the whole vector Pi in the multicast message

When a process Pj receives a message from Pi. It buffers the message untill **both**
   1. Pj is expecting msg.ts[i] as the next sequence from Process Pi
   ```python
   Pj[i] + 1 == msg.ts[i]
   ```
   2. Receiver Pj satisfies causality which was true at the time of send for Pi
   ```python
    for k != i
    Pj[k] >= msg.ts[k]
   ```
     After the above condition is satisfied logical clock needs to be updated before message delivery.
      * set `Pj[i] = max(msg.ts[i], Pj[i])`
      * Before delivery to app layer update self clock `Pj[j] += 1`  
*Note:* Causal ordering implies FIFO ordering



