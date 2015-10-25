# NovelWriter Architecture (working draft)

Novelwriter will be built using a service-oriented architecture (SOA). It will
consist of the following services:

* authentication
* accounts (user preferences, project metadata, etc.)
* manuscript storage/management
* social graph engine (characters <-> settings, etc.)
* frontend (single page app
* publishing service (exporting as pdf, ebook, html, etc.)

The frontend will authenticate users, manage authoring text, and submit text to
the manuscript service. The manuscript service will parse the text and submit
extracted semantic/contextual content to the social graph engine, which will
attempt to fit it into the graph model it's maintaining for the project.

The frontend will then read social graph data back in from the social engine
for user feedback/management, etc. The user can clean it up, add/remove things,
etc., which will allow the engine to be better at ingesting new data from the
manuscript service.

```text
frontend --> authentication
         --> accounts
         --> manuscript       --> social engine
         --> social engine
```

# Advantages

This architecture has the following advantages:

* It's suited to testing/experimentation, because you can feed existing/sample
  manuscripts to the service and validate it's parsed output.
* Similarly, you can validate the social engine because it doesn't know/care
  about text parsing or where its input comes from.
* The frontend remains decoupled from both services, and can be tested/demoed
  against a stub API, etc.
* The feedback loop from manuscript -> social engine -> user -> social engine
  *should* allow us to easily get good at extracting the right things from user
  input.
* Separating the engine and manuscript storage allows the user to either start
  writing first or designing first, and doing either will make the other
  experience better.
