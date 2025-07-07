# sas-user-manager

As a person who have been worked on SAS infrastructures and services almost all of my professional life, one of the common challenge for providing SAS service is authentication, authorizations, and user managements using JWT token for a user access.

I have seen may different solutions but they all comonnly have below issues.

1. Inflexibilities of JWT token
   in my experiences biggest problem with JWT token is that it is trying to solve both authenitcation and authorization at the same time and results.  i.e. when a user's scope changes triggered by authorization change, a new token must be issued as JWT token is immutable.  All the JWT token with previous scope is valid.  This is one of the troubling point of the user experiences.
2. [there is no good way to invalidate JWT token](https://stackoverflow.com/questions/21978658/invalidating-json-web-tokens)
   once a JWT token is created created it is valid and there is no good way to invalidate it.  i.e. if a user logged in and then user's access is removed then the token is techinicaly valid until it's experiation time without additional logic
3. mulitple JWT tokens per user
   because of aforemntioned issues, often there are multiple JWT tokens are used by a single user.  i.e. separate JWT token per a resource per user.  This further complicates user and access managements.
4. cost
   popular solutions such as okta and auth0 often cost more than $3000 per month for enterprise with various limits and restricitons

This project is my attempt at providing a generalized solution for SAS user managements, specifically focusing to address above issues.

## Key design philosophy

### Authenticaiton and authorization is a separate issue

Authentication, who you are, and authorization, what you can do, is a separate issue.  There are many solutions tries to be a silver bullet to solve both issues all the while being secure, i.e. JWT token.

Adhering to divide and conquor strategy, these problems should be divided and conquered separately as it creates confusion, complications and compromises.

### Idempotent changes

User management must be predictable and reliable

### Flexible

System should be able to easily handle user's authentication and authroization changes quickly without any delays or additional issues.

## Solution

### Use JWT token as as authentication only

JWT token is great of granting an access for a none machine use cases.  We would simply under utilize the `scope` of JWT token as authoriation is handled separatley and continue to use signed and verified payload of the JWT token payload.  Most importantly, user identifier which are user email.

### Authorization is handle separately using signed user identifier from JWT token

One JWT token is created, a token's authroization is evaluated and managed separately using a user's identifier from a JWT token.  In most cases it is an user email.

We can now associate `admin` or `read-only` accesses to the email `john@smith.com` separately.  

### Using cloudflare

cloudflare by far the easiest to use and cheapest cloud solution out there, especially [considering no ingress and egress cost](https://community.cloudflare.com/t/cloudflare-workers-as-data-transit-proxy-how-is-egress-ingress-data-charged/673586).

![alt text](auth-service.png "auth service")
