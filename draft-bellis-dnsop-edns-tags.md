---
title: DNS EDNS Tags
docname: draft-bellis-dnsop-edns-tags-01

ipr: trust200902
area: Internet
wg: DNSOP Working Group
kw: Internet-Draft
cat: std

coding: utf-8
pi:
  - toc
  - symrefs
  - sortrefs

author:
  -
    ins: R. Bellis
    name: Ray Bellis
    org: Internet Systems Consortium, Inc.
    abbrev: ISC
    street: 950 Charter Street
    city: Redwood City
    code: CA 94063
    country: USA
    phone: +1 650 423 1200
    email: ray@isc.org
  -
    ins: A. Clegg
    name: Alan Clegg
    org: Internet Systems Consortium, Inc.
    abbrev: ISC
    street: 950 Charter Street
    city: Redwood City
    code: CA 94063
    country: USA
    phone: +1 650 423 1200
    email: aclegg@isc.org

--- abstract

This document describes EDNS Tags, a mechanism by which DNS clients and
servers can transmit an opaque data field which has no defined semantic
meaning other than as previously agreed between the client and server.

--- middle

# Introduction

This document describes EDNS Tags, a mechanism by which DNS clients and
servers {{!RFC1034}} can transmit an opaque data field which has no
defined semantic meaning other than as previously agreed between the
client and server operators.

The tag is a single 16 bit field stored within the RDATA of an EDNS(0)
OPT RR as described in {{!RFC6891}}.

Two EDNS options are defined to allow for the detection of servers that
incorrectly echo responses verbatim.  The EDNS-Client-Tag option may
only appear in client requests, and the EDNS-Server-Tag may only appear
in responses from servers.

# Terminology

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
"SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and
"OPTIONAL" in this document are to be interpreted as described in BCP 14
{{!RFC2119}} {{!RFC8174}} when, and only when, they appear in all
capitals, as shown here.

# Description

The values of the individual bits within a tag are not defined to have
any semantic meaning in this specification.  Their interpretation is
defined entirely by bi-lateral agreement between client and server
operators.  The definitions for EDNS-Client-Tag and EDNS-Server-Tag
values MAY be different.

Operators are free to partition the bits within that field as they see
fit; for example it could be used to transmit up to 16 separate boolean
flags, or perhaps to transmit a 10 bit numeric value combined a 2 bit
value and four boolean flags.

The intended mode of operation is that the value of a bit (or range of
bits) could be tested in access control lists or any other such policy
control mechanism.

Possible use cases for EDNS-Client-Tags include:

- client-controlled selection of a DNS-based security filter

- marking a packet passing through a proxy with transport-related information

Use cases for EDNS-Server-Tags are still to be determined. The option is
specified here for symmetry and in anticipation of new use cases being
discovered.

## Packet Validation Rules

The OPT RR in a DNS request packet (QR = 0) MUST NOT contain an
EDNS-Server-Tag option.  A request packet MUST NOT contain more
than one EDNS-Client-Tag option.

The OPT RR in a DNS response packet (QR = 1) MUST NOT contain an
EDNS-Client-Tag option.  A response packet MUST NOT contain more
than one EDNS-Server-Tag option.

An EDNS-Server-Tag option MUST NOT be sent unless the corresponding
client query contained an EDNS-Client-Tag option.

## Error Handling

Clients MUST discard any response packet that breaches any applicable
packet validation rule.

Servers MUST respond with a FORMERR in accordance with Section 7 of
{{RFC6891}} on receipt of a request that breaches any applicable packet
validation rule.

## Wire Format {#wire}

The format of the EDNS options are as follows, to be stored within the
RDATA of an OPT RR as specified in {{RFC6891}}:

### EDNS-Client-Tag

                    +0 (MSB)                            +1 (LSB)
       +---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+
    0: |                       OPTION-CODE (TBD1)                      |
       +---+---+---+---+---+---+---+---|---+---+---+---+---+---+---+---+
    2: |                       OPTION-LENGTH (2)                       |
       +---+---+---+---+---+---+---+---|---+---+---+---+---+---+---+---+
    4: |                        CLIENT-TAG-DATA                        |
       +---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+

OPTION-CODE: The option code identifier (TBD1).

OPTION-LENGTH: Size (in octets) of OPTION-DATA.  MUST be 2.

CLIENT-TAG-DATA: The tag field sent from client to server.

### EDNS-Server-Tag

                    +0 (MSB)                            +1 (LSB)
       +---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+
    0: |                       OPTION-CODE (TBD2)                      |
       +---+---+---+---+---+---+---+---|---+---+---+---+---+---+---+---+
    2: |                       OPTION-LENGTH (2)                       |
       +---+---+---+---+---+---+---+---|---+---+---+---+---+---+---+---+
    4: |                        SERVER-TAG-DATA                        |
       +---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+

OPTION-CODE: The option code identifier (TBD2).

OPTION-LENGTH: Size (in octets) of OPTION-DATA.  MUST be 2.

SERVER-TAG-DATA: The tag field sent from server to client.

# Security Considerations {#security}

Client tags are under the control of the client software and as such
(and in the absence of any other mechanism to authenticate the client's
identity) this mechanism is not appropriate for applications where the
DNS server operator wishes to contractually differentiate service based
on the interpretation of the tag's value.

# Implementation status {#impstatus}

TBC.

# Privacy Considerations {#privacy}

Tags are opaque fields that encode only a limited amount of information.
The size of the data field in this specification is chosen to offer a
compromise between offering sufficient content to be technically useful
while also limiting the scope for it to be used to transmit Personally
Identifiable Information.

# IANA Considerations

IANA has assigned the following EDNS(0) Option Codes:

    Value    Name                  Status         Reference
    ----------------------------------------------------------
    TBD1     EDNS-Client-Tag       Standard       RFCXXXX
    TBD2     EDNS-Server-Tag       Standard       RFCXXXX

<< Note to IANA - please assign an even value to TBD1, and the next
consecutive odd value to TBD2.  This allows the least-significant
bit of the option value to be compared against the packet's QR bit >>

# Acknowledgements

The authors wish to particularly thank Brian Conry, Peter van Dijk and
Matthijs Mekking for early review and feedback on this document.

--- back
