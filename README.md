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

## Mission / vision

 - Provide **minimalist building blocks for game servers**
 - Be cloud-ready: reliable, scalable, observable, etc.
 - Adhere to the [twelve-factor app methodology](https://12factor.net/)

## Technical choices

 * services are implemented with nodejs
 * services rely on couchdb and/or redis for storing data:
   * couchdb for critical data, things we can't afford to loose
     * examples: user accounts, virtual currencies, etc.
   * redis for data that loosing is fine or just a minor annoyance
     * examples: authentication tokens, pending invitations, etc.
 * services support games running on the **browser** and native apps for **iOS** and **Android**

# Architecture

# Introductory case study

Before digging into proper specification, here's a small case study: playing a move on a turn based game.

1. load my list of games
2. load details for game X
3. play a move


# Security

API calls generally fall into one of those 3 access levels:

 * **public** - data everyone can read
 * **private** - user data only him is allowed to read or write
 * **internal** - data only accessible for read or write by other server modules

To achieve this, calls are protected using 3 mechanisms:

 * **API secret key**
   * secret string required for internal calls
 * **Authentication token**
   * secret string that identify a single authenticated user
 * **Impersonation**
   * specially forged authentication token with format `<api_secret>.<username>`
   * easy way for server modules to run requests on behalf of a user

# Users

Ganomede provides a layered user accounts management:

 - core account services that can be shared between games
 - the higher level app-specific users module

## Accounts

The module responsible for storing the user accounts is [ganomede-directory](https://github.com/j3k0/ganomede-directory). It is the lowest level in the users management stack.

It associates with each user a single unique identifier. It also stores a set of `aliases`: globally unique (key, value) pairs.

An example of alias is `email`. Only 1 user is allowed to have a alias "email" with value "user@email.com". For the client, it means referring the user by its email is guaranteed to map to a single ganomede user account. Other uses for this you can think of for aliases: Facebook identifier, display name, twitter handle, etc.

Check out ganomede-directory's [data structure](https://github.com/j3k0/ganomede-directory#data-structures) for more details on user accounts.

## Authentication

[ganomede-directory](https://github.com/j3k0/ganomede-directory) also provides authentication service. A user sends its id and password, he gets a authentication token in return.

It's also allows other authentication services to generate authentication tokens, by sending the API secret key instead of the password. For instance, Facebook login makes use of that functionality.

## Meta-data

Stored in [ganomede-usermeta](https://github.com/j3k0/ganomede-usermeta).

## Users

Explain the role of [ganomede-users](https://github.com/j3k0/ganomede-users).

## Tag mode

The so-called tag mode is a convention on how 2 specific aliases are registered in the ganomede-directory and their meanings.

In tag-mode, all users should have those 2 aliases:

 - `name`
   - which represents how usernames are displayed to the end users
   - can be changed by the user
 - `tag`
   - a confusing-letters-proof version of `name` (see [ganomede-tagizer](https://www.npmjs.com/package/ganomede-tagizer))
   - has to be changed when `name` changes

`name` is initially set to be equal to the user id, and so `tag` (by definition) is the tag build from the user id.

### Tag-mode service behavior

Services can be set to work in tag-mode, by setting environment variable `TAG_MODE` to any non empty string.

In tag-mode, for all incoming requests that accept a username, the tag is accepted as well. The client is responsible for translating `username` to `name`. Only when it's not possible, like for push notifications, the server will do the translation.
