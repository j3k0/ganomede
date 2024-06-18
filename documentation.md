# Overview

Ganomede is a anagram of "game node", it's a platform for building game servers on top of [NodeJS](https://nodejs.org).

It is a collection of micro-services, forming building blocks of a whole service. Currently, it has services for handling:

 - user accounts
 - notifications (including push notifications)
 - chat rooms
 - list of games
 - turn-based game logic
 - invitations (to whatever -- games, chat, ...)
 - system events
 - daily challenges
 - leader-boards and statistics
 - virtual currencies (and in-app purchases)
 - virtual items, player inventories
 - etc.

Ganomede targets games running on the **browser** and native apps for **iOS** and **Android**.

# Mission

Ganomede provides **minimalist building blocks for game servers**, with all the nifty feature you would expect: reliable, scalable, observable, etc.

# Introductory case study

Before digging into proper specification, here's a small case study: playing a move on a turn based game.

1. load my list of games
2. load details for game X
3. play a move

## Architecture

# Security

API calls generally fall into one of those 3 access levels:

 * **Public**
   * Data everyone can read
 * **Private**
   * User data only him is allowed to read or write
 * **Internal**
   * Data only accessible for read or write by other server modules

To achieve this, calls are protected using 3 mechanisms:

 * API secret key
   * The very secret string required for internal calls
 * Authentication token
   * A secret string that identify a user
 * Impersonation
   * A specially forged authentication token with format `<api_secret>.<username>`
   * Easy way for server modules to run requests on behalf of a user

# Users

Ganomede provides a multi-layered user accounts management.

## Accounts

The module responsible for storing the user accounts is [ganomede-directory](https://github.com/j3k0/ganomede-directory). It is the lowest level in the users management stack.

## Authentication

## Tag mode

## Meta-data
