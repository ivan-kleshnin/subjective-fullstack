# Caching

Lets recall that there are two categories of content (resources): static and dynamic.
Static content is generally tracked by VCS and so is release-bound. Dynamic content
is usually not tracked by VCS and so is release-unbound.

The primary difference we're interested in – is the frequency of updates.
**Updates of static resources are rare** – noone makes a new app release every hour.
**Updates of dynamic resources are frequent** – noone binds registrations (or purchases!) to the project release cycle.

The appropriate caching strategy should be based on the forementioned category and type (mime-type)
of a resource. So there are four basic caching strategies used in web:

1. No caching
2. Expirable cache
3. Conditional cache
4. "Eternal" cache + cache busting

Which type of resources are there? It depends on a business model. Is your site one-user (static blog, Jekyll-alike)
or multi-user? Do you allow users to upload images? Generally, it's like:

* HTML – dynamic or static
* IMAGES – static or dynamic
* STYLES and SCRIPTS – static (except for JSBin-alike...)

Now let's review our options.

### 1. No caching

First of all – for some file types caching may be enabled by default so "No caching" may be read
as "Disabled caching".

This category fits for dynamic content: API and pages of multi user site or app.

This category does not fit for static content. 10 requests for `style.css` on 10 page views would be a
disaster.

HTTP Header: `Cache-control: no store`

### 2. Expirable cache

This one is used almost exclusively as a performance optimization. You either cache for short periods
to deal with extra-traffic or use cache-busting (option 4). Long-term caching without cache-busting
is useless (how will you invalidate the cache on urgent demand?).

So this category fits for any content as long as period is short enough for you to tolerate release
suspensions (few minutes?).

HTTP Header:

* `Cache-control: $1, max-age=600` (10 minutes)

where `$1` stands for `public` or `private` (disallow caching by intermediate caches).

### 3. Conditional cache

Two options: `Last-Modified` and `ETag`.

`ETag` is hands down better. With `Last-Modified` you will have timezone. problems. You will have
problems for sites with many languages on single URL (different tag, same modification time). You
will experience more problems with clustering.

`ETag` fits for all content. For dynamic: it's +1 header for the chance of being cached.
For static – it's a big chance to strip the body of response entirely.

https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.3.5

HTTP Headers:

* `Cache-control: max-age: 60` (1 minute)
* `Etag: ...`

### 4. Cache busting

With this one you change resources' URLs per release. And enable cache on year ahead (more than year
can be ignored as invalid – as I've heard...). Then you're free from conditional requests as well.

Again, two options: `bundle.css?v2` and `/v2/bundle.css`.
Some people [say](https://www.stevesouders.com/blog/2008/08/23/revving-filenames-dont-use-querystring/)
that the latter is better because the first one is not compatible with reverse proxy caching.

Honestly, there's not enough confirmation for this. Even in that article (2008!) people were arguing against
the author opinion saying that most proxies allow such customizations and they gradually become defaults.
Other mentions of the topic I found were referring to the same article...

As querystring is so much easier to change I tend to ask – Why not just read a version from package.json
and put it there? Why invent webpack plugins for that?

[This post](http://webmasters.stackexchange.com/questions/52948/url-with-query-disables-caching/52950)
mentions a quirk with Firefox cache algorithm.

Quote:

> The Firefox disk cache hash functions can generate collisions for URLs that differ only slightly,
namely only on 8-character boundaries. When resources hash to the same key, only one of the resources
is persisted to disk cache; the remaining resources with the same key have to be re-fetched across
browser restarts. Thus, if you are using fingerprinting or are otherwise programmatically generating
file URLs, to maximize cache hit rate, avoid the Firefox hash collision issue by ensuring that your
application generates URLs that differ on more than 8-character boundaries.

Hovewer, at the moment of this writing, this quote is no longer available on the target site
(or searchable elsewhere)... Please let me know if you have a proof.
