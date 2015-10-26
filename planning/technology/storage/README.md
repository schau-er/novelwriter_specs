# Storage Requirements

This file documents the various requirements in storage for the project.

## Storage Groups

There are various groups of data that will need to be stored, and it could all be stored in the same place or in separate places. Also, the same data could be stored in multiple places.

1. Manuscripts
2. Manuscript Assets (i.e. characters, settings, etc.)
3. Manuscript Feedback and Editing Metadata
4. User Events (i.e. logins, clicks, etc.)
5. General User Info

## Manuscripts

This section details the requirements and options specifically for the manuscript.

### The Requirements

#### The Essential

1. The resulting interface must be trivial for a user to use
2. Resiliency - data cannot be lost
3. At least basic versioning - could be as simple as user instigated backups and copying project for branching
4. Flexible to change (i.e. we change how we structure projects, and it's not a total nightmare to do so)

#### The Bonuses

1. Git-level versioning
2. Git-level contribution attribution
3. Git-level branching
4. Commit-like notes on changes and progress

### The Options

#### Straight S3

##### Upsides

1. Cheap
2. Simple
3. Limitless Scalability

##### Downsides

1. Bare bones
2. Will require building our own content management layer
3. Updates could be painful

#### Git

##### Upsides

1. Powerful versioning control
2. Branches
3. Attribution to work
4. With commits, can track progress and go back to a previous state easily

##### Downsides

1. Unknown how practical git will be
2. Cheap storage - unknown how easily it will work with S3
3. To leverage the power of git, it will be a fine balance between power and simplicity
4. May be more power than the user needs

##### Potential Partners

Jack is thinking that really we probably want all of these:

1. git (for versioning)
2. S3 (for durability)
3. ElasticSearch (for real-time querying)

If we treat S3 files as immutable, they serve as our disaster-recovery solution
and easy rollback/restore source. Git repos are considered current 'source of
truth' versions, unless there's a catastrophe and we manually fall back to an
s3 snapshot. Elasticsearch is considered ephemeral, and gets completely blown
away on every update (or we store things with git SHAs, which allows us to
search by version, which could be cool). The git SHAs would be useful all over
the place for keeping track of state. If everything's tagged with the sha, we
don't have to worry so much about race conditions, etc. Could be massively
overkill.

One option would be to run our own git server (fairly trivial, though we'd
have to support dynamic user mapping), which our
manuscript storage service talks to, and on the git server we back up (cp) a
tarball of the repos either after every commit, or every hour, or whatever we
want. That would allow other services to fetch the latest version of the repo
from s3 and reconstitute it for local work, or what have you. It would also
mean we could theoretically provide git access to our users.

Here's an idea. We could set up a git server with git hooks to send the updated
content to the manuscript parser, which would then send the parsed items to the
social engine. Then we'd just have to figure out how to get our frontend
connected to git. Maybe that's just done by the api server calling the git
client.

In terms of scale, we could also explore AWS's new NFS product, which *should*
allow us to scale our compute nodes (for running the git server), because each
one of them could access any of the repos.

We could also do everything asynchronously, and just put the latest saved state
into a queue, which qprocs popped off and handled forking the content into git,
s3, the social enging, etc. Whatever we want, really. Setting that up might be
a lot of work.

There's a Ruby [gem](https://github.com/schacon/ruby-git) which supports
interfacing with git. Not sure about quality.

#### ElasticSearch

##### Upsides

1. Fun
2. Text search for free
3. Could be used in more traditional database use cases (i.e. storing characters and settings as JSON)

##### Downsides

1. Will require building our own content management layer
2. No versioning for free
3. Depending on how versioning is done, it could complicate searching if multiple versions are in db
