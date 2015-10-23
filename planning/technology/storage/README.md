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

1. S3 - storage for git
2. ElasticSearch - duplicate data to make searchable

#### ElasticSearch

##### Upsides

1. Fun
2. Text search for free
3. Could be used in more traditional database use cases (i.e. storing characters and settings as JSON)

##### Downsides

1. Will require building our own content management layer
2. No versioning for free
3. Depending on how versioning is done, it could complicate searching if multiple versions are in db