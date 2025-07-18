---
layout: post
title: Cursor Way of Pagination
excerpt: "The easy way to build cursor-based pagination in the application."
last_modified_at: 2022-11-27 14:17:42
categories: notes
tags: [sql, configuration, development]
image:
  feature: wordpress.png
comments: true
---

Many articles and notes have been written about the disadvantages of "regular" pages when browsing lists of content. The major disadvantages are the speed of SQL `OFFSET` and duplicates of "edge" elements.

The next generation of dynamically changing content (blog feeds, tweets, Instagram posts) requires advanced content navigation approaches. One of them is cursor-based pagination.

The idea of cursor-based pagination is moving between "next" and "previous" pages quickly, without duplicates or missing content. Usually users don't have "pages" navigation—it looks like infinite scroll on the feed page. Examples of this type of navigation are the infinite feeds of Twitter, Instagram, Facebook, etc.

Developers who integrate with this type of pagination also don't have "pages" in the API but have a "cursor" to the next or previous page. It looks like:

{% gist 60ba05b97448c9595434cb35ea4afb84 %}

## Implementation

The minimal model of any content is:
* **PRIMARY KEY** – unique item identifier, could be integer, uuid, or any other type;
* **DATA** – a field, or number of fields of "human" content;
* **ORDER** – a field, or number of fields to determine the order content should be displayed in;

> Modern platforms use algorithms to determine the order of the content for each user; this approach is out of scope for this article.

Since the **ORDER** field often is not unique, we can't use it exclusively. We need something more reliable than that.

So let's build the pagination of news feed. We'll use item created date as an **ORDER**.

The table **news** will look as follows.
```
 uid | title           | content                       | datetime
--------------------------------------------------------------------
 ... | World War Z     | Lorem ipsum dolor sit ...     | 1638004125
 ... | Etiam Molestie  | Maecenas vulputate mi ...     | 1624774080
 ... | Nam eu Lobortis | Mauris facilisis sagittis ... | 1614406080
```

Since *datetime* is not unique, but *ID* is, we need to create a single directional list of items using it.

The database index will help to chain items by those fields.
{% gist 5b4936232c1d8cae5f3c39f57376df2a %}

Now query to get the next page will be:
{% gist 22652f6ed9e4b05b009bc63d35d101a2 %}

Links to previous and next pages could be provided by the API as part of the response, but the application using the API could also build them on its own according to the latest response.

Getting the previous page is a little bit different:
{% gist 7f075577b3bb06524f7bdcc5409771b0 %}

So we change the order but then reverse the items before sending them as a response.
