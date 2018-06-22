# VeChain core nodes security checklist

> By SlowMist Security Team & Joinsec Team

### [中文版](./README.md)

## Table of Contents
* [Introduction To Security Program](#introduction-to-security-program)
* [Core Targets Of Framework](#core-targets-of-framework)
* [Major Problems VeChainThor Is Confronting](#major-problems-vechainthor-is-confronting)
* [Core Design Of Framework Architecture](#core-design-of-framework-architecture)
* [Core Defense](#core-defense)
* [Recommended General Framework](#recommended-general-framework)
    * [Detailed Explanation Of Each Part Of Architecture Design](#detailed-explanation-of-each-part-of-architecture-design)
        * [1. BootNode](#1-bootNode)
        * [2. MasterNode](#2-masterNode)
* [Security Enhancement Program](#security-enhancement-program)
    * [1. HTTP API Security](#1-http-api-security)
        * [1.1 Shielding HTTP API](#11-shielding-http-api)
        * [1.2 HTTP API Defense](#12-http-api-defense)
    * [2. Configuration Security](#2-configuration-security)
        * [2.1 Turn On Logging](#21-turn-on-logging)
        * [2.2 Set Maximum Number Of Connection Peers](#22-set-maximum-number-of-connection-peers)
        * [2.3 Avoid Using 'root' To Run 'thor'](#23-avoid-using-'root'-to-run-'thor')
        * [2.4 Listen To Random Ports](#24-listen-to-random-ports)
    * [3. Network Security](#3-network-security)
        * [3.1 Cloud Service Provider](#31-cloud-service-provider)
        * [3.2 DDoS Defense](#32-ddos-defense)
    * [4. Main Server Security](#4-main-server-security)
    * [5. Threat Intelligence](#5-threat-intelligence)
    * [6. NormalNode Core Security Configuration Summary](#6-normalnode-core-security-configuration-summary)
* [Acknowledgement](#acknowledgement)

## Introduction To Security Program
Given the fact that VeChain decentralized design does not strictly rely on one single node, the core design dedicates to ensuring there are certain amount of secure MasterNodes in the entire network.

## Core Targets Of Framework

1. Protect security of normal node's HTTP API.
2. Protect the communication and operation of MasterNode server.
3. Protect the communication and security of BootNodes in the preliminary stage.
4. Enhance the main net anti-attack capability in the initial phase.

## Major Problems VeChainThor Is Confronting

1. Targeted attack on BootNode and MasterNode, such as DDOS attack.
2. HTTP API abuse and unauthorized access to HTTP API functions.
3. Network communication failure.

## Core Design Of Framework Architecture

1. VeChainThor guarantees the high availability of BootNode list and deploys targeted defense approach.
2. Compared to EOS framework, VeChainThor lowers the cost of single node adequately, and increases the number of nodes, and guarantees the stability of MasterNode.

## Core Defense

1. By default, HTTP API is closed. When it's necessary to open the HTTP，VeChainThor messes up the port and sets DDoS defense protection.
2. Increases the number of BootNodes, and guarantee high recoverability after DDoS attack.
3. MasterNodes that are exposed to public network, are deployed with some anti-DDoS attack defense system, making sure the stability of entire network.

## Recommended General Framework

![Architecture](./images/arch.png)

Architecture explanation：

Given the feature of VeChain, the core design of this architecture is to make sure the robustness of the entire network.

For against the DDoS attack, main nodes (BootNode and MasterNode) should deploy DDoS defense system, protecting BootNode providing inilization service for normal nodes;

and

because of the limited number of nodes in the initial phase, the entire network is deployed with over 20 BootNodes, and targeted defense approach is deployed for these BootNodes (such as deploying DDoS defense system) so as to anti DDoS concentration attack. BootNodes, of which quantity exceeds 20, are hard to be brought down.

### Detailed Explanation Of Each Part Of Architecture Design

#### 1. BootNode
BootNode is a fixed network initialization node, used for discovering other nodes. The core defense strategy is to increase the number of nodes and distribute nodes to different network segments. It is suggested to use DNS (instead of fixed IP) nodes, and over 20 hard coded domains should be reserved for BootNode. In addition, nodes should only turn on P2P ports and deploy DDoS defense strategy.

#### 2. MasterNode

There are 101 MasterNodes in the VeChainThor network. VeChain official recommendation is to deploy MasterNodes in the internal network, and they are hard to be attacked because this part of master nodes are of a large quantity. But for MasterNodes whose protocols are exposed in the public network, it's necessary to deploy anti-DDoS defense strategy. 

## Security Enhancement Program

### 1. HTTP API Security

#### 1.1 Shielding HTTP API

It is suggested not to publish the HTTP API (Starting/Initialization without any manual settings/interference guarantees the privacy of http api).

#### 1.2 HTTP API Defense

It is not suggested to publish the node API to the public network, if it is needed to expose the HTTP API to the public, some protection procedures should be added prior to HTTP request. 

### 2. Configuration Security

#### 2.1 Turn on Logging

-It is suggested to set `--verbosity 9` initialization parameter for BootNode and MasterNode, so as to record the complete logs.

#### 2.2 Set Maximum Number Of Connection Peers

- Initialization parameter `--max-peers` is used to set maximum number of concurrent connection of P2P ports. The default connection number is 25 and it can be configured according to node's capability. Meanwhile, VeChainThor node holder can optimize the `ulimit` system parameter and kernel parameter, strengthening the resistance of continuous attack.

#### 2.3 Avoid Using 'root' To Run 'thor'

It is suggested to create a normal user account after compiling and use this account to run `bin/thor` instead of using root, for the purpose of relieving risks.

#### 2.4 Listen To Random Ports

- `--api-addr IP:PORT`
- `--p2p-port PORT`

Listen to one random port every time when starting the node, it is recommended to adopt configuration approaches from [Main Server Security](#4-main-server-security).

### 3. Network Security

#### 3.1 Cloud Service Provider

Throughout the tests of slow mist team, Google Cloud, AWS and UCloud have better performance on anti-DDoS attack. Moreover, these service providers will not temporarily close the server after DDoS attack and it is rather fast to recover the network availability, thus it is recommended to use these cloud services for core nodes (Pay attention to choosing cloud service providers: many cloud service providers temporarily close the server after DDoS attack).

#### 3.2 DDoS Defense

For coping with potential DDoS attack, it is suggested to deploy nodes in the internal network, if nodes are exposed to public network, DDoS protection should be deployed. It is suggested to deploy DDoS defense protection service, such as Cloudflare, and AWS Shield, for core nodes.  

### 4. Main Server Security

- Protect DDoS defensed Servers from being discovered and located in global scale network scans. Use the most popular ports instead of 11235 (and also HTTP API port: 8669), such as 80, 443, and 22, so as to significantly raise the identifying cost of attackers.
- Close the irrelevant service ports, and set strict security rules for AWS or Google Cloud.
- Change the SSH default port: 22, restrict the SSH to use only key, which is encrypted, to log in. Forbid using password to log in, and restrict that only our operation side's IP is able to access SSH.
- Under the situation of owning sufficient budget, it is suggested to deploy advanced HIDS (Host Instruction Detection System) (It is strongly recommended to take reference from the OSSEC open source project) to cope with server attack.

### 5. Threat Intelligence

- It is highly suggested to do collection, storage and analysis work of the related important logs. These logs include: the entire logs of communication between HTTP API and P2P port, the host server system logs, node related programs operation logs, etc. Storage and analysis work can be done by building applications with ELK (ElasticSearch, Logstash, Kibana) open source programs, or buying some advanced commercial platforms.
- Once choosing the mature cloud service providers, their control panels have many threat intelligence related modules that can be referred to, so as to discover exception in advance.
- Once serious bugs and attacking information happened at the node, the contingency plan should be immediately launched, which includes disaster preparedness strategies and upgrading strategy.
- Community information should be exchanged and shared.

### 6. NormalNode Core Security Configuration Summary
1. Choose reliable cloud service providers, such as Google Cloud, AWS and UCloud, to build node infrastructures. Server hardware should be configured with dual-core processor with 4G memory or more advanced.
2. Configuration rules forbid the access of server ports that is irrelevant to node service (SSH only enables credentials to access and only IPs from our operational side can connect to the SSH port).
3. When using root to start node, we start with `--p2p-port PORT`, enabling listening to the random P2P protocols, and if it's not necessary, VeChainThor just configures the HTTP API to merely listen to the local host.

## Acknowledgement

We would like to express great regards to

* VeChain Team

providing great support for node security testing.