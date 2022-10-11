# Pocket Network 1.0 Cross Module Integrations

<table style="text-align: center;">
<tr style="text-align: center;">
  <td style="border-collapse: collapse;">
    <p align="center">
        Alessandro De Blasis
        @deblasis<br>
    </p>
  </td>

  <td style="border-width: 0px;">
    <p align="center">
        Andrew Nguyen
        @andrewnguyen22 <br>
    </p>
  </td>

  <td style="border-width: 0px;">
    <p align="center">
        Daniel Olshansky
        @olshansky <br>
    </p>
  </td>

</tr>
</table>

<tr>
<td style="border-width: 0px;">
    <p align="center">
        Version 1.0.0<br>
    </p>
</td>
</tr>

## Overview

In this section, we will describe in which way the various modules interact with each other.

## Introduction

While we proceed in the R&D of the individual modules, we need to consider that even if we are developing them in a microservice fashion, they still need to communicate with each other so that the application and therefore the Protocol itself can function as expected.

The purpose of this document is not to highlight the capabilities of each model but to focus on the point of contact/interactions with a brief explanation about the motivation behind the integration under discussion.

## Integrations

### Bus

An application `Bus` serves as a cross-cutting integration medium.
Without going too much into technical details, its main features for the purposes of this document are:

- acts as a service discovery layer, allowing to get instances of a module from within another
- provides access to messaging functionality (EventBus)

### P2P - Persistence

#### P2P AddressBook management / Network Churn

Regardless of the underlying algorithm, `P2P` is a stateful business, there's state that has to be stored in memory (RAM) but there's also state that has to be persisted on the blockchain.

In particular, when it comes `Network Churn` where `Actors` such as `Validators` are involved, joining and leaving the network, P2P has to integrate with Persistence in order to keep track of which actors were part of the network at that point in time (`Height` in Protocol terms).

The running instance of the P2P module in a node, keeps in memory an "Address List" of the peers and that's being updated constantly.

Whenever the `Consensus` determines a new `Height`, an event via the `Bus` notifies its listeners (`P2P` included) that the list of the current `Actors` that are part of the network has to be stored in `Persistence`.

Essentially, in simple terms, we can now call a function that returns the list of the Validators at a certain `Height`.

This function is used to deliver messages that are relevant only for a specific `Height` to the relevant actors.