# Broadsheet.me

## Broad Idea

Broadsheet provides a single place where you can keep up to date on the main social networks, and interact with people on all of those platforms in a uniform manner. Content is "de-duped" so that you don't have to see the same thing from different networks. When you react to a social object, the reaction goes back via the most appropriate network (the source), and others' reactions from all "duplicate" items are aggregated back in a single view. The main "broadsheet" view is a Pinterest-inspired, Instagram fueled, buttery smooth pleasure to use. It is responsive down to a single-column, mobile-friendly river of items.

Creating new content is simple and intuitive, and automatically posts to wherever makes the most sense, based on the data included in the new item.

Search is provided across your own content (all connected networks, via local search), across your friends on each network (via local search), and globally (via remote API search).

Users all get a merged profile, which contains all of their content from all networks. Media is extracted from their items to provide a rich view into what they're interested in.

When logged in, you get a unified inbox for all direct/personal contacts across connected services, and a combined activity feed telling you what's happening that's relevant to you, again, across all services.

Lists of users may be managed across services, so you can keep track of a group of folks, regardless of what social accounts of theirs you know about. Lists are made up of people, so if you find out another account for a person, that's automatically included in the List.

A map view is also available as an alternate lens on most other views, showing only geo-encoded items and their respective locations.

## Architecture Notes

### Random Bits

* Socket connection from client back to server so that new content can always be streamed down
* All items are represented with a shared base set of information. Additional information may or may not be present, based on the service where that item came from
* All items stored as JSON documents in MongoDB
* There is a single, flexible template which handles items with/without location data, media items, etc
* No local concept of a user -- all tied to an existing external service (any of them). Connecting multiple services builds up the user-mapping graph.
* User mapping is global (so if I indicate 2 profiles are the same person, everyone gets that merge info)
* Is Elastic Search needed for the search backend?
* All services are contacted asynch, and new items are fed down the socket to the client where they are merged/updated "in-place"
* Animate new items into place with a "grow/fade" appearance (and reposition tiles)
* Masonry for layout
* Tiles should resize up to a max-width, and should fill the viewport. E.g. desktop might get 5+ tiles across, tablet will be 2-4 across, phone will probably be a single tile.

### Routes

    /
Homepage: Login buttons if not "logged in", otherwise your broadsheet view of whatever content we can load (using all default preferences).

    /map
Map view of above

    /:user
Load a single user's merged profile (summary at top, all content below). Use a Mongo ID until a user themselves picks what their URL should be (based on usernames of connected accounts).

    /:user/map
Map view of above

    /:user/items/:item
View a specific content item from a specific user. When this route is accessed via JS, it should load as a modal over whatever view is already visible. When accessed directly it should load `/:user` and then load the modal above it.

    /:user/lists/:list
View one of my specific lists of people.

    /:user/lists/:list/map
Map view of above
