# TODO List

- ganomede-events
  - [ ] deploy redis
  - [ ] deploy service
- ganomede-users
  - [ ] deploy
- ganomede-invitations
  - [ ] redeploy ganomede invitations
- ganomede-admin
  - [ ] display `name`
  - [ ] search users by `tag`
  - [ ] deploy
- ganomede-challenges
  - [ ] client display `name`
  - [ ] deploy
- ganomede-chat
  - [ ] client display `name`
- ganomede-coordinator
  - [ ] client translate opponents username
- ganomede-notifications
  - [ ] send message by tag
  - [ ] receive message by name
- ganomede-statistics
- ganomede-turngame
  - [ ] send notifications by name

OK ganomede-avatars
OK ganomede-data
OK ganomede-events
OK ganomede-files
OK ganomede-helpers
OK ganomede-mailchimp-worker
OK ganomede-registry
OK ganomede-rewards
OK ganomede-virtualcurrency

# Next steps

- [x] Decide on presentation of (userId,name,tag) external server
- [x] Create all github issues
- [ ] Create tests/rest-api.sh for all modules
- [ ] Update Triominos client
- [ ] Follow up Alexey

## Thinking

Ideal case:

 - The client doesn't have to know about the `userId`.
 - For all user-inputed incoming requests, `username` means either `tag` or `name`.
   - if `tag` or `name` doesn't match with someone in the directory, it means `userId`.
 - For all outgoing requests, `username` means `name`.

Analysis:

 - Login
   - tagize the inputed username. Load `tag` from directory. Fail? failback to stormpath.
 - Registration
   - as currently, username will be used as tag, name and id.
 - Sent invitation
   - `username` is taggized then checked against the directory
 - Received invitation
   - Received invitation contains `name`
 - Stored invitation contains what?
   - `name` for quick GET access
   - `userId` and fetch `name` each time?
 - Get the list of invitations
   - authToken -> `userId`
   - `username` -> `userId`
   - need to recover from a single `userId`
 - Avatars are linked with a `userId`
   - store avatars linked with a `userId`
   - allow tagizer(name) to load avatars

Simpler solution is to use `userId` all over the place... Let client translate this to a display name.

Wherever we store a "name", we can add logic later to translate that by using
`/directory/v1/users/alias/tag/:name`

However we have problems listing all entries for a given user (since it can have different names
at different moments in time).

Who is reponsable for translating userId => name? Maybe the client then.

Eventually. The server might only work with userId everywhere.

 - `userId` => `name` used when displaying.
 - `tag` => `userId` used after input.

# Steps

## Step 1

 - changes to ganomede-users:
   - public metadata takes `tag` as identifier
   - add `username` virtual metadata

Then we can use:

 - `/users/v1/:tag/metadata/name` to retrieve the displayname
 - `/users/v1/:id/metadata/username` to retrieve the username (userId)

DONE

## Step 2

 - create a ganomede-directory node library
