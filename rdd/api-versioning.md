# API Endpoint Versioning

## Common Approaches

- Version in URL
- Version in Header

## Opinions in the Wild

Interesting read from the Have I Been Pwned author: [Your API versioning is wrong, which is why I decided to do it 3 different wrong ways](https://www.troyhunt.com/your-api-versioning-is-wrong-which-is).

> 1. URLs suck because they should represent the entity: I actually kinda agree with this insofar as the entity I’m retrieving is a breached account, not a version of the breached account. Semantically, it’s not really correct but damn it’s easy to use!
>
> 1. Custom request headers suck because it’s not really a semantic way of describing the resource: The HTTP spec gives us a means of requesting the nature we’d like the resource represented in by way of the accept header, why reproduce this?
> 
> 1. Accept headers suck because they’re harder to test: I can no longer just give someone a URL and say “Here, click this”, rather they have to carefully construct the request and configure the accept header appropriately.

Another article, [Common Misconceptions about API Versioning](https://apigee.com/about/blog/developer/common-misconceptions-about-api-versioning), makes a nice argument in favor of using headers (content negotiation).

> HTTP offers a cleaner solution to the problem of offering users multiple formats for the same resource. It’s called content negotiation. You’re probably already familiar with the idea: when making an HTTP request, the client includes a number of headers that describe what format (media type in the jargon) they want the information back in, or what format they are providing data in.

I personally prefer the header choice. I have used version-in-urls in the past and didn't particularly like it, neither for the clients nor for the code base. 

For default version (no version provided), I'd serve a pinned version and upgrade it on a plan with proper notification time to API users, reserving the right to change the default whenever we wish.

## Real Cases

- AWS uses dates for versions, places them in the URL, reserves the right to change the default version freely
- GitHub uses the [`Accept` header](https://developer.github.com/v3/media/#request-specific-version), reserves the right to change the default version freely (contributed by @wzalazar) 

## Reference
1. https://blog.apisyouwonthate.com/api-versioning-has-no-right-way-f3c75457c0b7
