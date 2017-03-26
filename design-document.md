---
layout: page
title: Design Document
permalink: /designdoc/
---

## Section 1: Overview

TigerTrade is a platform that is widely-used (mostly by graduate students) for buying and selling goods. It last saw active development by Rodrigo Menezes ‘13, and is aging by the day. The goal of our project is to revamp TigerApps by reimplementing it on a modern stack with new features that would make it sufficiently appealing for undergraduates to use.

## Section 2: Requirements and Target Audiences

### Intended User Base

* **Graduate Students**: Currently, TigerTrade is already widely used by Princeton graduate students, so any USG-sanctioned drop-in replacement would need to accommodate for this user set first and foremost.

* **Undergraduate Students**: Additionally, most Princeton undergraduates currently use the Free & For Sale and Textbook Exchange Facebook groups.

    * Ideally, our TigerTrade implementation would capture a mass of Princeton undergraduates sufficient to supplant Free & For Sale.

### Benefits

Our system will benefit these users by offering them a more convenient or reliable way to buy and sell items from other users. We primarily aim to do so by reducing friction in the user interface and by providing better information to users.

#### Reduced Friction

Users should not need to jump through the number of hoops that currently exist when buying and selling items. We hope to reduce friction through heavy integration and intuitive interface design.

* **A Better UI**: Users should be able to browse through available items visually and fluidly, rather than based on text descriptions.

    * This is especially relevant for items like clothing, where images are a must.

    * Currently, Facebook offers this feature in a way that TigerTrade does not through image support.

* **Low Friction Seller Contact**: Users should not need to click into Facebook Messenger to express interest in a product.

    * The seller will be notified either by e-mail or a Messenger bot about the interest.

    * Potential buyers will be able to show interest in one click, with an optional message.

* **Facebook Messenger Integration**: Users should be able to interact with TigerTrade through a Facebook Messenger bot.

    * This reduces friction by allowing users to query without installing a new app for trading items.

    * The bot would also connect people who both use Messenger to initiate trading contact.

* **Venmo Integration**: Users should be able to pay using Venmo in one or two clicks.

    * This will allow collection of pricing data to improve market pricing, as well as simplify the payment process for users.

* **Facebook Free & For Sale Integration**: Allow users to interact with the Free & For Sale group entirely through TigerTrade.

    * This allows users to use our app without fear of losing a potential sale.

    * This also expands the number of items available for sale through the app before we hit critical user mass, making the app useful at any scale.

* **Buy Requests**: Users should be able to request to purchase items not currently listed.

    * This feature currently exists, but is almost never used. We would hope to analyze why this is so, and if there is compelling reason and means to, study how to make the feature more useful and compelling.

* **Saved Searches**: Users should be able to search for items and save the search so they can return to it easily, and receive notifications of new items that fit the search.

    * This feature currently exists in Free & For Sale, but is rarely used and buried deep in Facebook’s interface.

#### Better Information

Currently, information provided by users is extremely low-quality, and makes it difficult to make meaningful purchasing decisions. We hope to improve the quality of this data both by making it easier to give good information and to collect and generate better data ourselves.

* **Search**: Users will be able to search for items by category, similar keywords, etc. rather than the direct listing of the item, similar to Amazon.

    * Users should also  be able to search/filter by category, tag, price, and sold status.

    * Currently, Free & For Sale has no such functionality.

* **Market Pricing**: Rather than negotiate for prices, users should be able to agree on a market price range for the product to reduce friction.

    * Other options, such as auctions, already exist in TigerTrade and would continue to be supported.

    * A current problem with Free and For Sale is that people are unwilling to list initial prices for their items, which complicates sales.

* **Textbook Search**: Users should be able to search for coursebooks by course in a consistent, low-friction way.

    * We are aware that a number of other groups are considering working on this, so hopefully integration will be possible.

    * We would hope to replace the Textbook Exchange Facebook group.

## Section 3: Functionality

*Give some typical use case scenarios. If you work through some scenarios carefully, the rest of the system will be a lot easier -- this should be done before you worry about implementation details. Joel Spolsky's articles on **[specification*s](http://www.joelonsoftware.com/articles/fog0000000035.html)* are worth reading for a general guide.*

### I want to sell something (e.g. a chair).

I log into the site via CAS. When I go to post a listing, if I have not used TigerTrade before, the site encourages me to add other information - specifically, Venmo username and contact info (email address is already provided, but Facebook profile link or phone number as well). 

(** nice to have **) As I enter in the name of the item I’m selling, TigerTrade should search for similar instances of that product being sold, either on Amazon or previously on TigerTrade. It can then show those instances to me so that I can confirm whether or not the items are comparable. From comparable items, I can draw conclusions about what the market value of my item is so I have a good idea of how to price it. 

I can then post the actual listing, with price, description, image, and time limit, or set up an auction. For pricing: the site allows setting a specific price, indicate if the price is negotiable, or putting up the item for auction. 

From then on, I will be notified whenever a user indicates they are interested in my item, and can choose to respond to the request. . Once I’ve made a sale, I indicate that the item has been sold (and the price at which it was sold) so that the listing is removed from the website. 

### I want to buy something (e.g. a chair) that is listed on the site.

I log into TigerTrade and see some default listing of items for sale--potentially, the most recent items posted; the "hottest" / most-viewed active listings; listings about to expire; or listings that match search queries I’ve made before.

I click on the search box and type in a search query for an item. I can click into an advanced search view to select tags or categories that apply to the item I’m looking for as well as restrict results by price range, time, or other factors, but by default queries are text searches that match item name first, description text second. The search results return as a list of listings featuring item names, images, descriptions, and any tags or categories that apply. Once I click into a listing, I can read the seller’s description, view any additional pictures, and contact the seller. 

### I want to subscribe and be notified if someone makes a listing matching some parameters.

The first part of this user flow is the same as for buying an item listed on the site. After I have searched for an item I’m looking for, I’m offered the option to set up a search subscription--if new items are posted that match my search terms (to some level of relevance), I can be notified via email or text message TigerPounce-style should something I want become available. I can also post an open offer to buy an item (e.g. a couch) to encourage potential sellers to get in contact with me. 

## Section 4: Design

### Technology Stack

We will be using Go with the Buffalo system for our backend, and a React single-page application on the frontend. The data will be stored in a Postgres database, with models as we describe below. Finally, we will also be integrating with the Facebook Messenger, Venmo, and Amazon APIs to accomplish the respective features listed above.

### Data Design

We have the existing TigerTrade database, and would like to use that existing data so that we can run as a drop-in replacement for the system.

**Item**

* Title

* Description

* User ID

* Type [Auction or Direct]

* Price

* Status [Open, Sold, etc.]

**Item Tags [Association]**

* Item ID

* Tag ID

**Photos**

* Item ID (Foreign Key)

* Photo URL

* Caption

**User**

* Name

* NetID

* E-mail

* Venmo Login Token/Username

* Facebook Login Token

* Preferences

Search

* Query String

* Last Updated

* User ID

### Data Transfer

The interface between the middleware and the frontend will consist of a REST API with the following approximate endpoints and usage:

**Items (Full Resource)**

Items will serve as a standard RESTful resource to represent all items. The resource will allow for filtering by price, categories, timestamps, condition, and seller rating.

**Market Prices (GET)**

This resource will be GET-only and return pricing recommendations algorithmically determined using Amazon, and previous pricing data.

**Users (Full Resource)**

As with any user-based app, we will have a RESTful resource to represent users as needed. This resource will allow us to use JOINs to in the items to retrieve seller data.

## Section 5: Timeline

**Week 0 - Spring Break (3/24)**

* Get familiar with existing TigerTrade permission, especially the data model

* By end of spring break: starting implementation of backend (aka have REST API for basic MVP), or at least stub code

* Have data model completely specced out; migrate over data if necessary

    * Specs for backend schema

    * Specs for RESTful interface to frontend

* Data typing code in Go

* Plan what extra data we’ll need for search

**Week 1 - 3/31 - get project status website up by March 27; weekly TA meetings start**

* UI for displaying listings

* Working on search during this time...

**Week 2 - 4/7**

* UI for posting a listing

* Working on search during this time...

**Week 3 - 4/14 - project prototype due -**

* We should have our MVP by this point - in our case, a drop-in replacement for TigerTrade replacing existing features (basic search, price listings, 

* Should have figured out how searching will work

**Week 4 - 4/21**

* Fix broken things from prototype

* Tag suggestions

* Nice-to-haves: textbooks, price comparisons

**Week 5 - 4/28** - alpha test up

**Week 6 - 5/5** - beta test

## Section 6: Risks and Outcomes

**Learning Go**: Go is a new language for everyone on the team, so idioms and common practices may be out of reach for most of us. However, we do believe we can seek good mentorship and resource (such as Prof. Kernighan’s *excellent* book on the language), and failing this, we can always fall back to Node.js, which many of us have extensive experience in.

**User Privacy and Ethics**: While we will try to limit our data access as much as possible, we are dealing with sensitive information, and would need to develop ways to handle and ethically use the data we are given. We will consult with professors and researchers in CITP to take care of this. Furthermore, handling abuse and fraud may be difficult to handle in practice. If a user gets scammed in the system, we need to figure out ways to protect ourselves legally.

**Venmo Integration**: We caught wind that Venmo’s API is being severely restricted, so integration will be difficult at best. However, we think there should be ways around this without violating the ToS, and failing this, we can fall back to PayPal integration.

**Ideal Outcome**: Ideally, we hope that TigerTrade replaces the Free & For Sale for undergrads and TigerTrade for graduates. This is good for both groups because right now undergraduates are not aware of what the graduate students are selling and vice versa, and there’s no reason that they shouldn’t be able to trade with each other.

