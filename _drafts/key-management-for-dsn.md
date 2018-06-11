---
layout: post
title: Key-Management Scheme for Distributed Sensor Networks
tags: [Most Cited]
---

Distributed Sensor Networks (DSNs) are ad-hoc mobile networks that have sensor nodes with limited computational power and communication capabilities. One of the important characteristics of a DSN is to allow for dynamic addition and deletion of sensor nodes after deployment. This helps the network to grow easily and to replace failing nodes. One of the deployment scenario for these DSN is in hostile areas and hence they need a secure mechanism for communication, sensor capture detection, key revocation and sensor disabling for when a sensor node gets compromised or detected.

![nodes](/assets/img/posts/MostCited/nodes.png)

DSNs differ from traditional Embedded Wireless Networks (EWNs) in the following categories:
1. There are more number of sensor nodes in DSN as compared to EWN.
2. Nodes have to be added and deleted dynamically in DSN.
3. DSN is also deployed in hostile regions to gather intel.

## Communication Security Constraints

Sensor nodes in DSN have limited memory, computation capability, battery, and communication range. Hence, running a desktop based secure communication algorithm in DSN would not be a good idea. For instance, Carman, Kruus, and Matt \[[1]\] report that on a mid-range processor, such as the Motorola MC68328 “DragonBall,” the energy consumption for a 1024-bit RSA encryption (signature) operation is much higher than that for a 1024-bit AES encryption operation; i.e., about 42 mJ (840 mJ) versus 0.104 mJ. Hence, using an asymmetric cryptosystem approach would lead to a high performance impact for securing communication.

So instead, symmetric-key and hash functions are employed in WSN to protect inter-sensor communication.

## Key Management Constraints

Since the final topology of DSN is unknown prior to deployment, *key pre-distribution* is the only practical approach to date for key distribution. Keys have to be installed in sensor nodes before deployment for secure communication. There are two methods of *key pre-distribution*:
1. Single *mission key*, where all the sensor nodes have the same key. This is an inadequate solution because if a sensor node is captured, then the entire communication of the DSN is compromised.
2. Another method is to have separate *(n - 1)* keys, each being pair-wise privately shared with other nodes which should be installed in every sensor node. Here, unlike *mission key* method, selective key revocation of captured sensor node becomes a possibility. However, this method requires each sensor node to store *(n - 1)* keys which is impossible as DSNs are huge and sensor nodes have limited memory which has to be used for gathering sensor information. Moreover, direct node-to-node communication in DSN is only possible with a limited number of neighbors depending on communication range and sensor density. On the other hand, since all the sensor nodes have *(n-1)* keys, a network-wide broadcast is needed for key revocation in this particular scenario.

---

## Proposed Approach

The proposed approach consists of a simple key pre-distribution scheme that requires memory storage for only a few keys and provides the same operational properties when compared to pair-wise private key sharing scheme. This scheme relies on probabilistic key sharing among nodes of a random graph and uses a simple shared-key distribution, revocation and node re-keying \[[2]\].

### Key Distribution

Key distribution consists of three phases namely key pre-distribution, shared-key discovery, and path-key establishment.

The *key pre-distribution phase* consists of generation of a large pool of *P* keys and their key identifiers; random drawing of *k* keys out of *P* without replacement to establish key ring of a sensor; loading the key ring into the memory of each sensor and associated sensor identifier on a trusted controller node; and for each node, loading the i-th controller node with the key shared with that node.

The *shared-key discovery phase* occurs during DSN initialization in the operational environment where every sensor node discovers its neighbors by simply broadcasting in plain text the associated key identifiers of their key ring. A link exists between two sensor nodes only if they share a key and if a link exists then all the communication on the link will be secure. Also, it is possible that multiple links use the same shared key for securing communication.

The *path-key establishment phase* assigns a path-key to a selected pair of sensor nodes in wireless communication range that does not share a key but is connected with two or more links at the end of the shared-key discovery phase. There is no need for sensor nodes to generate new keys. According to the number of keys *k* in the key ring, a few of the keys are left unassigned and these can be used to establish a link between sensor nodes that are not connected during *shared-key discovery phase*.

### Revocation

When a sensor is compromised, it is essential to revoke the entire key ring for securing DSN communication. For key revocation, a controller node broadcasts a single revocation message containing a signed list of the key identifier corresponding to the one that has to be revoked.

Once a node receives this signed key identifier, it verifies the signature and finds the corresponding key identifier in its own key ring and deletes revoked keys if any. Once the revocation process is completed, it is possible that some links are broken and the *path-key establishment phase* is performed again. This is possible because not all the keys in the ring key of each sensor node are used.

### Re-Keying

It is possible that in some cases the lifetime of keys expires and re-keying must take place. Re-Keying can be considered as self-revocation of a key by a node. Hence, this does not involve any broadcast message and the shared-key discovery, path-discovery phase needs to be performed again.

### Resilience to sensor node capture

Although the proposed scheme is more resilient as compared to a single mission key or pair-wise private sharing of keys, physically capturing a sensor node can enable the adversary to steal some keys. In single mission key scheme, all communication links are compromised once a sensor node is captured. In pair-wise private keys, all the *(n - 1)* links of the captured node are compromised. Whereas in the proposed scheme, only k << n keys of a single key ring are discovered by the adversary and has a probability of approximately k/P to attack successfully any DSN link.

---
## Simulation Results

The simulations assume a network of 1,000 nodes with an average density of 40 sensor nodes in a neighborhood.

![1-1](/assets/img/posts/MostCited/dns-1.png)

The figure above shows that the average path length of the network depends on the size of the key ring. According to the figure, if *k* is small then there is a higher probability that a link does not have a key.

![2](/assets/img/posts/MostCited/dns-2.png)

According to the figure above, with k = 75, only half of the neighbors are reachable over a single link, but most of the other nodes may be reachable over in three-link paths. For k = 50, this increase to a maximum of four-link paths.

![3](/assets/img/posts/MostCited/dns-3.png)

The figure above shows that, out of the pool of 10,000 keys, only 50% of the keys are used to secure links, only 30% of the keys are used to secure one link, 10% are used to secure two links, and only 5% of the keys are used to secure 3 links. All the results mentioned here are from reference \[[2]\].

---
## Conclusion

The discussed key management scheme is simple, scalable, flexible and does not occupy much of the node's memory. This scheme is superior when compared to mission key and pair-wise private key method.

---
## References
\[1\] D. W. Carman, P. S. Kruus and B. J. Matt, "Constraints and Approaches for Distributed Sensor Network Security".
\[2\] Eschenauer L., Gligor V., "A Key-Management Scheme for Distributed Sensor Networks".

[1]: http://people.cs.vt.edu/~kafura/cs6204/Readings/SensorNetworks/SensorNetSecurity-NAILabs.pdf "Constraints and Approaches for Distributed Sensor Network Security"
[2]: http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.19.9193&rep=rep1&type=pdf "A Key-Management Scheme for Distributed Sensor Networks"
---
