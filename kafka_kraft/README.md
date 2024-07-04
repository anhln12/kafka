# kafka - is place to install & discuss about kafka internals
Step by Step Installation walk-through https://www.youtube.com/watch?v=DjbWM6Jr13Q&t=2489s

**kafka-3-cluster kraft (3 nodes kafka kraft cluster)**

Apache Kafka Raft (KRaft) is the consensus protocol that was introduced to remove Apache Kafka’s dependency on ZooKeeper for metadata management.

Benefits

- Better getting started and operational experience by requiring to run only one system.
- Removing potential for discrepancies of metadata state between ZooKeeper and the Kafka controller
- Improves stability, simplifies the software, and makes it easier to monitor, administer, and support Kafka.
- Allows Kafka to have a single security model for the whole system
- **Provides a lightweight, single process way to get started with Kafka**
- Makes controller failover near-instantaneous
- Simplify the configuration
- The Producer enables the strongest delivery guarantees by default (acks=all, enable.idempotence=true). This means that users now get ordering and durability by default.

<img width="539" alt="image" src="https://github.com/anhln12/kafka/assets/18412583/4a3d1b7e-b680-40b8-9934-c0d7c6fbc9aa">

• A major feature that we are introducing with 3.0 is the ability for KRaft Controllers and KRaft Brokers to generate, replicate, and load snapshots for the metadata topic partition named __cluster_metadata. This topic is used by the Kafka Cluster to store and replicate metadata information about the cluster like Broker configuration, topic partition assignment, leadership, etc. As this state grows, Kafka Raft Snapshot provides an efficient way to store, load, and replicate this information.
