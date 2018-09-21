# Interledger Network Roadmap

This document is broken into two parts, the first is a roadmap of operational changes that need to be made for the Interledger network to scale and become the robust and reliable payments infrastructure we want it to be. The second part of the document is a roadmap of specific technology updates that are required to help realize that goal.

## Operational Roadmap

The following sections outline "operational challenges" (OC's) and follow with "operational deliverables" (OD's) that are intended to address these challenges.

The OC and OD items are numbered so they can be referenced from the issue tracker.

Following discussion between the connector operators the proposal is to target a 4-tired architecture:
 - Tier 1 should be exchanges
 - Tier 2 will be back-bone connectors (provide most of the liquidity)
 - Tier 3 will be hosters (ISPs)
 - Tier 4 are users (senders/receivers)

### Challenges:

- **OC1**: Address allocation:
Only tier 1 and select tier 2 connectors should use top-level addresses. Vanity addresses are pointless as nobody actually sees them. If every connector uses a top-level prefix then routing tables and routing will not scale.

 - **OC2**: Backbone stability
We require a group of geographically distributed tier 1 connectors that are run with “carrier-grade” operational processes. They need to demonstrate commitment to both uptime and liquidity guarantees.

 - **OC3**: Onboarding
We require a structured means of onboarding new connectors to the network such that they understand what their obligations are and how to be responsible members of the network.

 - **OC4**: Defined Processes
We require some clear guidelines on how to deal with various network eventualities such as dropped links, loss of liquidity, new peering requests, stale routes, software updates, network wide configuration change requests etc.

 - **OC5**: Security
The security of the network has not been challenged to date. We need to begin putting some proactive measures in place to counter known attack vectors and deal with malicious network participants.

### Plan

Below is a list of "Operational Deliverables" OD's, that will help us to address the challenges above:

#### OD1: Establish an Interledger Exchange and Registry

This will replicate many of the functions of an IX on the Internet today. Initially it will likely be run by the tier 1 connector operators and over time will scale out to have multiple geographically dispersed hubs.

Example: INX ZA is a community run exchange in South Africa with geographically distributed POPs in 3 different cities. It is a useful model to replicate at a global level for ILP.

https://wiki.inx.net.za/

The exchange provides important services to ISPs in South Africa such as a route collector and looking glass service. The ILP exchange should provide similar services to connector operators.

Also see www.ripe.net for an example of how this is done on the Internet.

NOTES:

 1. The deliverable includes technical aspects as it is likely that the existing `ilp-connector` service will need significant changes to operate as an IX.
 2. We also have the challenge of needing liquidity for exchanges and deciding if they should offer FX.

#### OD2: Define Exchange Policies

We need to define policies for joining the exchange that cover acceptable use of the exchange services. The architecture we are striving for is tiered and it is important that anyone wishing to operate in higher teers understands the committment this entails. It is also important that there is an objective set of criteria that an operator can use to evaluate a peer and decide if they should be peered as a `child`, `peer` or `parent`.

#### OD3: Provide Technical Guides

It is important to continue developing technical guides for connector operators at all tiers.

There are already some guides available at https://ilp-ix.link, these need to be fleshed out any missing guides identified and published.

#### OD4: Expose Route and Network Data

Rather than building sophisticated front-ends the exchange should expose free data services that allow others to build value-added services for the network's users.

### Questions:

Do we need to establish a single IX or does it makes sense to collaborate on the tech required and leave each tier 1 operator to run their own?

Can we collaborate on https://ilp-ix.link/ as a starting point?

------------

## Technical Roadmap

The following sections outline "technical challenges" (TC's) and follow with "technical deliverables" (TD's) that are intended to address these challenges.

The TC and TD items are numbered so they can be referenced from the issue tracker.

### Challenges

 - **TC1**: Config changes require restart
The connector is unable to hot-load config which means a complete restart is required to alter configuration.

 - **TC2**: Connector and Plugins run in same process
The current architecture doesn’t allow plugins to be run in their own process which impacts the ability to scale the services and the deployment options.

 - **TC3**: Connector controls settlement
The separation of concerns is a little off in the current architecture. While the connector needs a view of the account balance for the application of some logic (e.g. throttling) it shouldn’t be the source of truth for the balance, this should be the plugin which is ultimately responsible for settlement which is the process that changes the balance. (The Ethereum plugin supports client settlement, as will Lightning. The change needs to be made for XRP as well.)

 - **TC4**: Plugins are difficult to test
It is difficult to test plugins fully including their ability to handle failure cases that are experienced in the wild.

 - **TC5**: No testnet support
There is no active testnet right now. Now that Interledger is multi-currency, it would be really helpful to have a test network before deploying to the mainnet. Moreover, this would be useful for Hackathons and other events to get interested parties hacking on ILP.
Every connector needs a load balancer (LB)
This unnecessarily complicates scaling. This is a greater architectural change, but parsing BTP packets at the LB would allow for multiple connector instances behind one LB.

 - **TC6**: Every connector needs a load balancer (LB)
This unnecessarily complicates scaling. This is a greater architectural change, but parsing BTP packets at the LB would allow for multiple connector instances behind one LB.


### Plan

At this point the plan is to capture specific product changes into a Github project within this repo. This is still pending.
