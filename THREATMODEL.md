# Threat model

Here we will discuss the potential threats to a Django app. Some we will
ultimately try to mitigate, otherwise we will not. By nature this will be a
rather generic analysis, as we are not analyzing a specific applications.

## Generic Description of Target Web App

**Motivation:** To provide a service to users which respects their privacy.
Data should only be available to those who the user explicitly selects. This
excludes the application owners by default.

A secondary motivation is to negate or mitigate data breaches, thereby
providing peace of mind to the application's owners.

**Application Usage:** A wide variety of technical competencies. Some form of
user authentication will be required, but this need not personally
identify the user. *The user may wish to share data either publicly, or with
selected other users or groups*.

**Legal requirements:** No specific legal regulation outside of data-protection
law (i.e. no PCI compliance).

**Availability requirements:** Three nines or better (9.99%).

**Technologies:** Database backed, Python 2/3.

**Assets of high value:** User data.

## Threat Sources

### Potential threat sources

* Application administrators (disgruntled employees)
* Data loss (laptop left on train)
* Automated attacks (script kiddies)
* Nation states
* Friends / family (reading your secret online diary)

### Major threats

* *Theft of entire database:*
    * Application admins - dump data from database
    * Data exposure - Misconfiguration, accidentally exposed backup/dump, lost USB stick
    * Automated attacks - SQL injection, XSS exploits (application-level attacks)
    * Nation states - being compelled by law
* *Theft of individual auth credentials:*
    * Friends / family / partners - offline theft of login details
* *Server compromise:*
    * Automated attacks - Network-level access, SSH access.
    * Application admins

### List of potential Attacks

* Admins dumping database [mitigation goal: **complete**]
    * Encrypt sensitive data using secret known only to the data owner.
* Accidental data exposure [mitigation goal: **complete**]
    * Encrypt sensitive data using secret known only to the data owner
* Application-level attacks [mitigation goal: *partial*]
    * Application-level attacks which result in database exposure
      can be protected against. However, attaches related to CSRF,
      XSS, phishing etc are outside the package's remit.
      *Alternative: django-security.*
* Disclosure as compelled by law [mitigation goal: *partial*]
    * Disclosure would require the interception of the user's secret,
      thereby requiring the user to login.
      *Alternative: unknown.*
* ~~Offline theft of login details~~ [mitigation goal: *none*]
    * Outside this package's remit
      *Alternative: User education in password security*
* Network-level unauthorized server access [mitigation goal: *partial*]
    * Data-theft would require the interception of the user's secret
      *Alternative: Network-level security, AppArmor etc*
* Administrator authorized server access [mitigation goal: *partial*]
    * Data-theft would require the interception of the user's secret
      *Alternative: Operational partitioning (i.e. minimize access by humans)*

Encryption could be done either on the client using JavaScript, or on the
server. As client side-encryption is entirely at the mercy of any JavaScript
provided by the server, there is no added benefit to performing the
encryption on the client. Therefore encryption should be performed on the
server.
