# API Endpoint Versioning

We need a mechanism that allows us to make backwards-incompatible changes to public-facing APIs, balancing agility and continuous improvement with the clients' need of a stable contract.

## Common Approaches

- Version in URL
- Version in Accept Header
- Version in Custom Header

## In the Wild

Interesting read from the Have I Been Pwned author: [Your API versioning is wrong, which is why I decided to do it 3 different wrong ways](https://www.troyhunt.com/your-api-versioning-is-wrong-which-is).

> 1. URLs suck because they should represent the entity: I actually kinda agree with this insofar as the entity I’m retrieving is a breached account, not a version of the breached account. Semantically, it’s not really correct but damn it’s easy to use!
>
> 1. Custom request headers suck because it’s not really a semantic way of describing the resource: The HTTP spec gives us a means of requesting the nature we’d like the resource represented in by way of the accept header, why reproduce this?
> 
> 1. Accept headers suck because they’re harder to test: I can no longer just give someone a URL and say “Here, click this”, rather they have to carefully construct the request and configure the accept header appropriately.

Another article, [Common Misconceptions about API Versioning](https://apigee.com/about/blog/developer/common-misconceptions-about-api-versioning), makes a nice argument in favor of using headers (content negotiation).

> HTTP offers a cleaner solution to the problem of offering users multiple formats for the same resource. It’s called content negotiation. You’re probably already familiar with the idea: when making an HTTP request, the client includes a number of headers that describe what format (media type in the jargon) they want the information back in, or what format they are providing data in.

[Using the Accept Header to version your API](http://labs.qandidate.com/blog/2014/10/16/using-the-accept-header-to-version-your-api/) expands a bit more on this particular approach, and explains:
> ```
> GET /jobs HTTP/1.1
> Host: api.example.com
> Accept: application/vnd.example.api+json;version=2
> ```
> 
> The vnd. part is dictated by [rfc4288-3.2](https://tools.ietf.org/html/rfc4288#section-3.2), and is used to tell standard media types apart from custom ones. A vnd.* media type can be submitted to IANA to be registered as official media type. In theory, you could just use application/json here and add the version parameter, but as [the json standard](https://tools.ietf.org/html/rfc4627#section-6) doesn't allow any parameters, it wouldn't be correct to use that.

In [API Versioning Has No “Right Way”](https://blog.apisyouwonthate.com/api-versioning-has-no-right-way-f3c75457c0b7) 
Phil Sturgeon goes over some of the most popular choices and then proposes _API Evolution_:

> API Evolution is the concept of never breaking your contracts until you absolutely absolutely have to, then when you do you manage that change with sensible warnings to clients. It is not about making arbitrary changes and breaking stuff.
>
> [...] when backwards compatible issues absolutely could not at all be avoided, we took advantage of the fact that a business name for a concept had changed, and took the chance to make our API match the business name.

This approach is very limited though, and may not be applicable in many situations.

## Well Known Cases

- AWS uses dates for versions, places them in the URL, reserves the right to change the default version freely
- [GitHub](https://developer.github.com/v3/media/#request-specific-version) uses `beta` and `v1` through `v4` for versions, places them in the `Accept` header and reserves the right to change the default version freely  
- [Facebook's marketing API](https://developers.facebook.com/docs/marketing-api/versions/) uses semver-like tags such as `v2.2` and places them in the URL

## Team's Experience

Everyone in the team has experience or knows a bit about the version-in-url approach.

## Alternatives

In the future we could do more research and put more thought on this topic. Some approaches to keep in mind:

- Supporting an _endpoint feature_ system, rather than global or endpoint versioning.
- For authenticated requests, storing the version of the API to use in the database for each user, allowing them to change it from an administration panel. 

## Conclusion

Following GitHub's approach, placing the version in the `Accept`header is the best option at the moment. It suits our current needs, requires no changes to the URLs, is already well documented and widely used.

We will tag the current version as `v1`, and it will be the default initially and for a period of time. Once `v2` has been out for a while, the default will be switched to it, and so forth.

The length of that period of time will be decided manually for each particular case, initially, until the business requires a formal definition of it. 
