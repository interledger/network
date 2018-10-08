# Proposal

Use the XRP Ledger (and other distributed ledgers) as the roots of trust for keys and data associated with entities on the Interledger network.

## Background
When two ILP systems connect they need to establish a secure trusted connection. This requires the connecting entity (client) to use some configured information to first discover the service endpoint of the other entity (server) and then verify that it is communicating with the correct entity before establishing a secure encrypted connection.

Further, when sharing route and other related information it is necessary for entitites to sign data that is passed on to thrid-parties who need to verify the source and integrity of the data.

## Current Process
Traditionally the client system will be configured with the name of the server using DNS. The client performs a DNS lookup and then connects to the service endpoint using the IP address it has resolved from the DNS name.

Upon connecting the client and server complete a handshake whereby they exchange certificates that prove their identity. Client certificates are often used a way to authenticate the client. During this process the entities prove that they “own” the certificate by signing a challenge with the private key that corresponds to the public key in the certificate.

Typically the certificates that are exchanged contain the DNS name of the entity, other identifying information (such as the company name) and public keys and signatures over this data. 

The client must ensure that the DNS name it used to establish the connection matches the certificate, that the other information is as expected, and that the certificate is valid. The server ensures that the client (identified by the certificate) is authorized to access the service.

A valid certificate is part of a chain and contains signatures from other certificates higher in the chain providing proof of its position in this hierarchy. The client must validate that the certificate chain has a valid Certificate Authority root certificate as the root of its chain.

This is the weakness of the current Public Key Infrastructure (PKI). Certificate Authorities are fallible and validation relies on the client having a set of known CA roots that have not been compromised. As such it is possible that even if a certificate chain appears valid that it may be a fake provided by an attacker or that the keys used in the certificate have been compromised.

## Web of Trust
The alternative to a known, trusted, set of Certificate Authorities is a Web of Trust where the validation of a certificate is done by evaluation of assertions by other trusted entities that they trust the certificate. In this way a web of entities with bilateral trust relationships is established as opposed to a fixed set of root CAs at the top of a hierarchy.

The idea of a Web of Trust has been around for a long time. Many systems use such a pattern for validation of public keys however the Web of Trust model is extremely difficult to bootstrap and especially so in an ecosystem where there is no directory service that assists with discovery of certificates, keys and entities.

## XRP Ledger
XRP Ledger provides an excellent utility to use as the source of truth and the decentralized directory for a Web of Trust. Using the XRP Ledger an entity can bind a domain, a ledger address and a messaging public key to each other.

An account_root node on the XRP Ledger includes properties for storing a domain and a messaging public key so these can be authoritatively bound to a ledger address as the primary identifier. (i.e. Only someone with the ability to modify the account_root can change the domain and public key).

### Verifying that a Public Key belongs to an entity

 1.	An entity will identify themselves by their XRPL address. 
 2.	An entity that wishes to connect to a service endpoint owned by that entity will be configured with both the service endpoint address (DNS name) and also the entity’s XRPL address.
 3.	The client will connect to the endpoint at the configured address (which includes a DNS name) and will complete a TLS handshake to establish a secure session. (This will include validating that the server certificate binds the DNS name being used and the public key presented and that the certificate is from a trusted chain)
 4.	Following this the client can further verify that the public key presented by the endpoint is the same key that is stored in the XRP Ledger under the account root for the entity it is connecting to (and that the DNS name being used is correct).

*NOTE*: It’s possible that the client skips traditional certificate validation completely and just uses the XRP Ledger as the source of truth in the case where the server doesn’t want to use certificates rooted at a CA.

### Verifying that a domain belongs to an entity

If an entity uses an XRPL address as a primary identifier it can set up an address that is likely to never change. The ability to disable the master key and add and rotate signers on the address makes an XRPL address significantly safer to use as a long term identifier than DNS names.

The fact that the address is owned by the entity as opposed to being rented from ICANN is also a significant advantage to this approach.

That said, the majority of security protocols today do use DNS names as identifiers, therefore the use of an XRPL address as the primary identifier with the ability to bind it to a DNS name as the secondary identifier is very powerful.

The long lived nature of XRPL addresses also allows clients to separate their secure configurations into those that may change regularly (service endpoint addresses that depend on DNS names and certificates) and those that should never change (the XRPL address of the service host) and can store the latter in a highly privileged configuration space.

This has significant security and operational advantages.

## XRP Ledger Web of Trust

While not the intended purpose, trustlines on the XRP Ledger can also be used to delineate a Web of Trust between Ripple addresses.

When evaluating the data on an account root (Message Key and Domain) a client may also look at the incoming trustlines to that address to determine which other trusted entities trust this XRPL address.

This would be a deviation from the intended purpose of trustlines (for the purpose of payments) and may prove to be a dangerous overloading of the function without changes to the data that is expressed on a trustline. This would need to be explored further.

## DIDs

The use of an XRPL address as an identifier for banks and ILSPs could prove to be preferred to BIC Codes both because they can be verified in a decentralized directory, but also because they are not under the control of a central authority that must be incorporated in a single country. As such the system is neutral with respect to legal jurisdiction and has no impact on the sovereignty of participants.

Further, if the system is extended to include businesses (as BIC Codes have been) the Web of Trust system could be a very powerful tool for validation of business data. 

This DLT based identity concept has significant support however predominantly in the domain of self-sovereign identity for individuals. It is possible that many of the standards being developed for this use case could also be applied to the business identity use case with XRP Ledger or other blockcahins as the roots using a standard such as DIDs.

See https://github.com/WebOfTrustInfo/rwot7/blob/master/topics-and-advance-readings/did-primer.md#a-short-primer-for-decentralized-identifiers for more info on DIDs
