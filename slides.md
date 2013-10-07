Evolutionary APIs
=================
## By Dave Caraway ##
<br>
### [Fogmine](http://www.fogmine.com) ###
[dave@fogmine.com](mailto:dave@fogmine.com)





What is REST?
=============
![Alt sleep](images/sleep.jpg)


REST
----
* software architecture for distributed hypermedia systems
* **NOT** a software package, protocol or specific technology
* Avoids custom protocols, just uses HTTP


Wait! What's hypermedia?
------------------------
<br>
Well come back to this topic. First...


Digression: Some background on HTTP...
--------------------------------------


[HTTP](http://www.w3.org/Protocols/)
------------------------------------
* Hypertext Transfer Protocol
* A request-response architecture
* Building block of the Internet as we know it
* Normally over TCP/IP transport protocol, but can support others as long as they are reliable
* Headers are visible, body is encoded in an envelope with associated Content-Type
* ***Trivia***: Fielding co-wrote the HTTP standard
* ***More trivia***: HTTP isn't the most used protocol. Do you know what is? [Answer](http://www.bittorrent.com/)


HTTP Request
------------
<br>
### Request Header ###

* request method
* URI
* protocol version

<br>
### Request Body ###

* request modifiers
* client information
* (possibly) body content


HTTP Request Example
--------------------
<br>
### Request ###

    POST /animals  HTTP/1.1

### Request Header ###

    User-Agent: Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/30.0.1599.66 Safari/537.36
    Content-Type: application/json; charset=utf-8
    Accept: */*
    Accept-Encoding: gzip,deflate,sdch
    Accept-Language: en-US,en;q=0.8

### Request Body ###

    {
    "animal": "aardvark",
    "description": "wow this thing really likes ants. how cool!"
    }


HTTP Response
-------------
Server responds with status code and (possibly) a response body


HTTP Response
-------------
### Response Header  ###

    HTTP/1.1 201 CREATED
    Date: Wed, 2 Oct 2013 17:59:13 GMT
    Server: Apache/1.4.27
    Content-Length: 345
    Content-Type: application/json

### Response Body ###
    {
        "id": 194,
        "url": "http://foo.com/animals/194",
        "animal": "aardvark",
        "description": "wow this thing really likes ants. how cool!"
    }


CRUD
----
* Uses HTTP methods
    + **POST** (Create)
    + **GET** (Read)
    + **PUT** (Update)
    + **DELETE** (Delete)
* HTML only supports **GET** and **POST** [(see forms description)](http://www.w3schools.com/tags/att_form_method.asp)


PUT can sometimes be used to create, if the API allows
------------------------------------------------------
<br>
Should create a new resource at the requested URI if supported

        PUT /some/resource/{requestedid} HTTP/1.1


Other HTTP methods
------------------
* ***PATCH***: Partial update, not requiring full representation (and thus less bandwidth)
    + ***Caveat***: not idempotent and some clients do not support patch


Other HTTP methods
------------------
* ***OPTIONS***
    + Indicates what HTTP methods can be used on a given resource
    + May include a response body, although HTTP 1.1 doesn't require it


Other HTTP methods
------------------
* ***HEAD***
    + Like **GET**, except no response body
    + Used to test links. Returns response codes, Content-Type and other header information


Other HTTP methods
------------------
* Put everything else behind a ? (in a ***query string***)
* Note: There are some remaining methods (TRACE, CONNECT...), but we don't use them


Safe Methods
------------
* should never change a resource representation on the server
* GET and HEAD are safe methods


[Idempotence](http://restcookbook.com/HTTP%20Methods/idempotency/)
------------------------------------------------------------------
* property that N>0 identical requests yields the same result as initial application
* `a=4;` is idempotent
* `a++;` is not idempotent
* GET, HEAD, PUT and DELETE are idempotent
* POST and PATCH are not idempotent


Idempotence is pretty important
-------------------------------
It says why you shouldn't insert data using a GET method


Query Strings
-------------
* multiple parameters joined by '&'
* filters (boolean operators)

        http://www.petshop.com/cats?name=fluffy&color=black&age=6&order=1

* query (full text search)
    + usually starts with 'q'

            http://www.petshop.com/cats?q=fluffy+black+kitten&order=1


[URL Encodings](http://www.w3schools.com/tags/ref_urlencode.asp)
----------------------------------------------------------------
* URLs only support the ASCII character-set
* Unsafe characters are replaced with a % followed by two hexadecimal digits (0..F)
* Can't contain spaces, replaces with + sign

        #Filter for cats named Günter with black fur, age 6
        http://www.petshop.com/cats?name=G%FCnter&color=black&age=6

        #Search books for "fluffy black kitten"
        http://www.petshop.com/books?q=fluffy+black+kitten


And back to REST...
-------------------


Source of REST
--------------
* Originated in chapter 5 of [Roy Fielding's Ph. D. Dissertation](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)
* Fielding's description left many unanswered questions, resulting in contention over what is and is not REST
* Many sources of information on REST have been informal: mailing lists, wikis and weblogs


Intuition
---------
* Based on idea that a good design will indicate its own use
* Reduces developer reliance on documentation
+ Enables reusable libraries and tools
+ Allows new developers to start using an API almost immediately


Resources are the building blocks of REST
-----------------------------------------
* Resources are just sources of information on the web
* They're the ***R*** in U***R***L
* Originally, just static documents and files on the web
* Now anything that can be identified, named, addressed or handled is a resource


In REST, resources are nouns (usually plural)...
------------------------------------------------


So you ask for resources like this...
-------------------------------------


Request
-------
![Alt fox](images/fox.jpg)

## Request Header ##

    GET /foxes


and receive a response like this
--------------------------------


Response
--------
### Response Header ##
    Content-Type: application/json
    Content-Length: 119
    ...

### Response Body ###

    {
        "foxes": [
            {
              "id": 20,
              "name": "fluffY"
            },
            {
                "id": 21,
                "name": "bubbles"
            }
        ]
    }


Resources can also be nested...
-------------------------------


So you can have a call like this
--------------------------------

Request
-------
![Alt fox](images/fox.jpg)

## Request Header ##

    GET /foxes/sounds


and get something like this
---------------------------

Response
--------
![Alt Fox](images/ylvis_fox.png)

### Response Body ###

    {
    "sounds": [
        {
            "id": 1,
            "phrase": "Ring-ding-ding-ding-dingeringeding!"
        },
        {
            "id": 2,
            "phrase": "Wa-pa-pa-pa-pa-pa-pow!"
        }
    ]
    }


Finite State Machine Model
--------------------------
* Applications modeled as an engine that moves from state to state by alternative transitions
    + State: where user currently is (current resource)
    + Transitions: instructions on user's next steps (e.g. links / forms from current resources to others)


Finite State Diagram
--------------------

![Alt finite state machine](images/state_machine.gif)


Dialogs in REST are stateless
-----------------------------
![Alt do_i_know_you](images/do-i-know-you.jpg)


States
------
* This means that every request made to the API should contain everything the application needs to service the request
* Client should be given a few (lets say, less than 10) well published endpoints from which the API can be navigated
* Client should not store anything that isn't well published endpoint
    + URLs could be changed by service provider


States & Transitions
--------------------
* Given a state and request, the server will determine the next state and generate a response


Example HTTP Request & Response
-------------------------------
[Twilio API](https://api.twilio.com/2010-04-01)

### Request Header ###

    Content-Type: text/plain; charset=utf-8 
    Accept: */*
    Accept-Encoding: gzip,deflate,sdch
    ...
  
### Response Header ###

    Content-Type: application/xml 
    Content-Length: 213 
    ...


Response Body
-------------

    &lt;?xml version="1.0" encoding="UTF-8" ?&gt;
    &lt;TwilioResponse&gt;
      &lt;Version&gt;
        &lt;Name&gt;2010-04-01&lt;/Name&gt;
        &lt;Uri&gt;/2010-04-01&lt;/Uri&gt;
        &lt;SubresourceUris&gt;
          &lt;Accounts&gt;/2010-04-01/Accounts&lt;/Accounts&gt;
        &lt;/SubresourceUris&gt;
      &lt;/Version&gt;
    &lt;/TwilioResponse&gt;


Communicating State
-------------------
* If the client needs to carry forward state from one request to another, use links
* Encode application state into URIs (small size)
* If state is large or can't be transported for security or privacy reasons, store in durable storage (db, filesystem) and encode reference to state in URI


[Links Relations](http://www.iana.org/assignments/link-relations/link-relations.xhtml)
--------------------------------------------------------------------------------------
* expressed by <code>rel</code> parameter of a link
* expressed as URI, such as <code>http://www.example.org/rels/</code> or registered name (e.g. rel="stylesheet")
* provide informational resource such as HTML at the URI, with document describing semantics of link relation. Include details such as HTTP methods supported, representaiton formats supported for requests and responses, and business rules about using the link
* there are a number of common rel types
* Relations fit in [two categories](http://tools.ietf.org/html/rfc5988#section-4)
  + Registered
    * Compare character by character
    * Case insensitive
    * MUST NOT constrain the available representation media type
    * Can specify behaviors and properties (can specify the behaviours and properties of the target resource (e.g., allowable
HTTP methods, request and response media types that must be supported).)
  + Extension
    * URI that uniquely identifies the relation type
    * Can also be expressed as a [Curie](http://www.w3.org/TR/2009/CR-curie-20090116/) or other format that can be converted to URI
    * Points to definition of semantics of the relation type


Common Registered Link Relations
--------------------------------
<table>
    <tr>
        <td>self</td><td>For links to preferred URI of this resource</td></tr>
    <tr>
        <td>alternate</td><td>For links to URI for alternate version of same resource</td></tr>
    <tr>
        <td>edit</td><td>Link to URI for clients to edit this resource</td></tr>
    <tr>
        <td>related</td><td>Link to a related resource</td></tr>
    <tr>
        <td>previous or next</td><td>Link to previous or next resource in an ordered series of resources</td></tr>   
    <tr>
        <td>first or last</td><td>Link to first or last resource in an ordered series of resources</td></tr>
</table>


You have to identify yourself and your resource, every time
-----------------------------------------------------------
(some overhead, but helps your API scale)


Identity often provided using API key
-------------------------------------
* Auth SID  = asdflkjalsdf20938092340923049
* Auth TOKEN (another long number)
* Commonly uses [HTTP Basic Auth](http://en.wikipedia.org/wiki/Basic_access_authentication) over HTTPS
        # Call to https://{AccountSid}:{AuthToken}@api.twilio.com/2010-04-01/Accounts
        POST /2010-04-01/Accounts/{TestAccountSid}/SMS/Messages


Stateless No Nos:
-----------------
![Alt Cookie Monster](images/cookiemonster.jpg)
* ***DO NOT*** Store state in cookies
* ***DO NOT*** create or rely on sessions
* ***DO NOT*** store auth tokens in the cookie


State Transfer Example1 Part1
-----------------------------

## Request ##
    POST /quotegen HTTP/1.1
    Host: www.example.org
    Content-Type: application/x-www-form-urlencoded
    fname=...&lname=...&..

## Response ##
    HTTP/1.1 200 OK
    Content-Type: application/xml;charset=UTF-8
    &lt;quote xmlns:atom="http://www.w3.org/2005/Atom"&gt;
      &lt;driver&gt;
        ...
      &lt;/driver&gt;
      &lt;vehicle&gt;
        ...
      &lt;/vehicle&gt;
      &lt;offer&gt;
        ...
        &lt;valid-until&gt;2009-10-02&lt;/valid-until&gt;
        &lt;atom:link href="http://www.example.org/quotes/buy?quote=abc1234"
              rel="http://www.example.org/rels/quotes/buy"/&gt; 
      &lt;/offer&gt;
    &lt;/html&gt;


State Transfer Example1 Part2
-----------------------------              
## Request ##
    POST /quotegen HTTP/1.1
    Host: www.example.org
    Content-Type: application/x-www-form-urlencoded

    fname=...&lname=...&..

## Response ##
    HTTP/1.1 200 OK
    Content-Type: application/xml;charset=UTF-8
    &lt;quote xmlns:atom="http://www.w3.org/2005/Atom"&gt;
      &lt;driver&gt;
        ...
      &lt;/driver&gt;
      &lt;vehicle&gt;
        ...
      &lt;/vehicle&gt;
      &lt;offer&gt;
        ...
        &lt;valid-until&gt;2009-10-02&lt;/valid-until&gt;
        &lt;atom:link href="http://www.example.org/quotes/buy?quote=abc1234"
              rel="http://www.example.org/rels/quotes/buy"/&gt; 
      &lt;/offer&gt;
    &lt;/html&gt;
                  

Example 2: Hiring System, Part 2
--------------------------------
## Request to enter candidate info ##
    POST /hires HTTP/1.1
    Host: www.example.org
    Content-Type: application/json

    {
      "name": "Joe Prospect",
      ...
    }

## Response containing a link to post reference checks ##
    HTTP/1.1 201 Created
    Location: http://www.example.org/hires/099
    Content-Location: http://www.example.org/hires/099
    Content-Type: application/json

    {
      "name": "Joe Prospect",
      "id": "urn:example:hr:hiring:099",
      ...
      "link" : { 
        "rel" : "http://www.example.org/rels/hiring/post-ref-result",
        "href" : "http://www.example.org/hires/099/refs"
      }
    }


Example 2: Hiring System, Part 3
--------------------------------
### Request to enter first reference comment ###
    POST /hires/099/refs HTTP/1.1
    Host: www.example.org
    Content-Type: application/json

    {
      "text" : "Joe is a ...",
      "by" : "...",
      "on" : "2009:10:12T16:05:00Z"
    }

### Response ###
    HTTP/1.1 200 OK
    Content-Location: http://www.example.org/hires/099
    Content-Type: application/json

    {
      "name": "Joe Prospect",
      "id": "urn:example:hr:hiring:099",
      ...
      "link" : {
        "rel" : "http://www.example.org/rels/hiring/post-ref-result",
        "href" : "http://www.example.org/hires/099/refs"
      }
    }

### Request to enter second reference comment ###
    POST /hires/099/refs HTTP/1.1
    Host: www.example.org
    Content-Type: application/json

    {
      "text" : "Worked with Joe, ...",
      "by" : "...",
      "on" : "2009:10:12T17:00:00Z"
    }

### Response ###
    HTTP/1.1 200 OK
    Content-Location: http://www.example.org/hires/099
    Content-Type: application/json

    {
      "name": "Joe Prospect",
      "id": "urn:example:hr:hiring:099",
      "refs": ...,
      ...
      "links" : [{ 
          "rel" : "http://www.example.org/rels/hiring/add-ref-result",
          "href" : "http://www.example.org/hires/099/refs"
        },
        { 
          "rel" : "http://www.example.org/rels/hiring/add-background-check",
          "href" : "http://www.example.org/hires/099/bgchecks"
        }]
    } 


Example 2: Hiring System, Part 4
--------------------------------
### Request to submit background check results
    POST /hires/099/bgchecks HTTP/1.1
    Host: www.example.org
    Content-Type: application/json
    {
      "text" : "...",
      "by" : "...",
      "on" : "..."
    }

### Successful background check
    HTTP/1.1 200 OK
    Content-Location: http://www.example.org/hires/099
    Content-Type: application/json;charset=UTF-8

    {
      "prospect" : {
        "name": "Joe Prospect",
        "id": "urn:example:hr:hiring:099",
        "refs": ...,
        "link" : { 
          "rel" : "http://www.example.org/rels/hiring/make-offer",
          "href" : "http://www.example.org/hires/099/hire"
        }
      }
    } 


Designing Resource Names
------------------------
* based on nouns, not verbs
* two urls per resource
    + collection (e.g. /polls )
    + element in collection (e.g. /polls/{id})


Designing Resource Names
------------------------
* Prefer plural over singular nouns for collections

        /polls
        /birds
        /aardvarks

rather than

        /poll
        /bird
        /aardvark


Designing Resource Names
------------------------
* Prefer concrete over abstract names
    + /dog rather than /pet
* For non-resources, use verbs not nouns
    + e.g. /convert or /render
* Moving content: redirects and permanent move codes


SEARCH
------
* scoped (particular to a resource, make a verb of that resource)


Limiting Results
----------------
* Use field specification in URL to limit fields returned
* Pagination
    + Use link headers [(see RFC)](http://tools.ietf.org/html/rfc5988)
    + [Example (blog post)](http://blog.steveklabnik.com/posts/2011-08-07-some-people-understand-rest-and-http)
* global (use search verb)


Format
------
* REST is independent of Format
* Most REST apis are JSON, XML, or HTML, but RSS, ATOM and other formats are just as viable


Format
------
* Ways of indicating prefered format
    + Included as part of the URL
* e.g. /foo.json or /foo.xml


## Format
+ Content Negotion
    * Content type specified in the request
    * HTTP Header (accept type)
    * Use camel-case naming conventions for JSON attributes
        + e.g. 'createdAt' rather than 'CreatedAt' or 'created_at'
        + why? because you can use the . ('dot') operator
        + myjson.createdAt rather than myjson['some_bad_name']


Errors
------
* Use appropriate non-2xx status code and message
* [HTTP Error Status Code (integer)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)
* Error messages (text) describing the problem
* Twilio Example
        &lt;TwilioResponse&gt;
            &lt;RestException&gt;
                &lt;Status&gt;401&lt;/Status&gt;
                &lt;Message&gt;Authentication Error - No credentials provided&lt;/Message&gt;
                &lt;Code&gt;20003&lt;/Code&gt;
                &lt;MoreInfo&gt;https://www.twilio.com/docs/errors/20003&lt;/MoreInfo&gt;
            &lt;/RestException&gt;
        &lt;/TwilioResponse&gt;


Versioning
----------
* Version your APIs!
* Many options
  + Version the URL
    * api/v1/foo
    * see https://api.twilio.com/
  + [Use Accepts header and a MIME type](http://barelyenough.org/blog/2008/05/versioning-rest-web-services/) like application/yourcompany.v1+json


Authentication
--------------
* Always use SSL
* Options:
    + HTTP Basic
    + OAuth via an Authentication Header
    + Via parameter
* Can have multiple authentication types supported (Github API supports all of the above)


Stop Here
---------
Incomplete slides follow


[Richardson Maturity Model](http://martinfowler.com/articles/richardsonMaturityModel.html)
-------------------------------------------------------------------------------------------
* 99.99% of "REST" APIs aren't REST
* Four levels
  + Level 0: Swamp of POX -- Few endpoints, use HTTP transport as tunneling mechanism for RPC style invocations
  + Level 1: Resources -- Communicate to resources
  + Level 2: HTTP Verbs -- Get out of the box in Rails or similar framework
  + Level 3: Hypermedia Controls -- Zen! You've reached HATEOAS
* [HATEOAS: Hypertxt as The Engine of Application State](http://timelessrepo.com/haters-gonna-hateoas)
    People would consider it ludicrous if they had to remember a dozen URLs to navigate a website, so why do we expect the consumers of our APIs to do so as well?


[Why HATEOAS](http://www.slideshare.net/trilancer/why-hateoas-1547275)
----------------------------------------------------------------------
* Resources evolve over time
  + Naming, URI, location, partition
* Assumptions about server resources break eventually
  + URI pattern, valid state transitions
* HATEOAS reduces assumptions
  + Loose coupling between client & server
  + Allow both to evolve independently
* Caveat: loose coupling does mean less efficiency (transport or otherwise)
* Not using HATEOAS: If you're absolutely sure something will never change(e.g. /login) then let everyone assume it forever


[Standards versus Pragmatism](http://blog.steveklabnik.com/posts/2011-08-07-some-people-understand-rest-and-http)
-----------------------------------------------------------------------------------------------------------------
* Engineering is the art of proper trade-offs: there's times when it's not the right decision to follow the specification
  + Choosing a non-standard way due to informed decision is totally okay
  + Making a decision based on ignorance is not okay
* There are lots of other valid architectures and REST isn't the best for all situations (e.g. real-time requirements) 


Hypermedia
----------
* Per Fielding, required for "REST"
* Describes internet of things
  + Machine readability
  + Semantic Web
* Mike Amundsen (Wrote "Building Hypermedia APIs with HTML5 and Node" )
* Mike Kelly (creator of HAL)
* Special Media Type (MIME Type)
  + vnd/collection+json
  + vnd/hal+json
  + vnd/siren+json


Type Marshalling Delimna
------------------------
1. Shared Schema 
2. URI Construction
3. Payload Decoration
4. Narrow Media Type


Shared Schema
-------------
* Publish document listing arguments and interaction details. 
* Disadvantages: schema changes break clients as schema information not updated dynamically


URI Construction
----------------
* Have URI carry type information, easy to generate URI from metadata in service code
* Popular
* Easy for frameworks using templating and other processing based on type
* Drawback: Lose control of URIs
  + Changes may break cached (bookmarked) links.


Payload Decoration
------------------
* Add (meta) to htm, "type" to JSON, etc
* Process data based on type hint. 
* Drawback: payload tightly bound to source code; changes break existing clients if not realizing new payload info required.
4. Narrow Media Type: (JAX-RS) @Consumes("application/stockquote+xml") Full control over URI & payload, but any independent client would have great difficulty keeping up with changes


Narrow Media Type
-----------------
* (JAX-RS) @Consumes("application/stockquote+xml") 
* Full control over URI & payload
* Drawback: any independent client would have great difficulty keeping up with changes





Let's Design
============
![Alt Design bed image](images/cartoon-bed-med.jpg)


Identify Resources
------------------


Create a transition diagram
---------------------------
![Alt Transition Diagram](images/api_states_and_transitions.png)





What are some helpful tools for REST?
=====================================
![Alt tools](images/Tools_clipart.jpg)


Documentation
-------------
* [Swagger](https://developers.helloreverb.com/swagger/)
  + Example: [Petstore](http://petstore.swagger.wordnik.com/#!/pet)
* [Apiary.io](http://apiary.io)
  + [Apiary Client - Ruby Gem](https://github.com/apiaryio/apiary-client)
* [IODocs](https://github.com/mashery/iodocs)
  + Example: [Rotten Tomatoes](http://developer.rottentomatoes.com/iodocs)


Code libraries and SDKs
-----------------------
* Ruby
    + Rails
    + Sinatra
* Javascript
    + Express.js
* Java
    + Jersey
    + Restlet
* Python
    + Flask
    + Django
    + Bottle


Misc
----
* Browser
  + [Advanced REST Client (Chrome)](https://chrome.google.com/webstore/detail/advanced-rest-client/hgmloofddffdnphfgcellkdfbfbjeloo?hl=en-US)
* [HAL Browser (for Hypermedia APIs)](https://github.com/mikekelly/hal-browser)
* [online JSON Editor](http://www.jsoneditoronline.org/)
* Curl (Standard commandline tool)

        curl -i -H "Content-type: application/json" -X POST http://api.foo.com/comment -d '
        {
        "subject":"Test Subject",
        "body": { "und": [ { "value": "Test Comment" } ] }
        }
        '





How can I learn more about REST?
================================
![Alt Learn More Yoda](images/STAR-WARS-YODA_510.jpg)


Study Examples
--------------
* [Twilio](http://api.twilio.com/)
* [Twitter](http://www.twitter.com/api)
* [Github](http://www.github.com/api)
* [CIMI, a nice hypermedia-based REST design](http://dmtf.org/sites/default/files/standards/documents/DSP0263_1.0.1.pdf) 


Reading / Videos
----------------
* [RestFest Videos](http://vimeo.com/channels/restfest2013)
* Mark Nottingham posts on 
  + [why to consider linking an HTTP API](http://www.mnot.net/blog/2013/06/23/linking_apis)
  + [HTTP API Complexity](http://www.mnot.net/blog/2012/06/25/http_api_complexity_model) 
* [JSON Home, a homepage for HTTP APIs](http://tools.ietf.org/html/draft-nottingham-json-home-03)


Follow Community Thought Leaders
--------------------------------
* Roy Fielding
* Mike Amundsen
* Mike Kelly
* Steve Klabnik
* Mark Nottingham


Joining the hypermedia mailing list
-----------------------------------
* 1st email will join you to list, [click here](mailto:hypermedia@librelist.com)





Q & A
=====
### [@davecaraway](http://twitter.com/davecaraway)
![url](images/qrcontactinfo.png)