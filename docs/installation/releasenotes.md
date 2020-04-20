---
layout: default
title: Release Notes and Upgrade
parent: Installation
nav_order: 1
permalink: /docs/installation/releasenotes
---

# Release Notes and Upgrade Instructions

KHEOPS is composed of a number of Docker Images. All the docker images belonging to a single version and meant to run together are taged with the same version number. As a general rule, upgrading versions is simply a matter up updating the tag on the docker images. **Addition steps** that need to be performed are detailed below. KHEOPS uses [Liquibase](https://www.liquibase.org) to manage the database schema, and the database will be automatically upgraded.

#### As with any ungrade - make sure to perform a backup of the database used by KHEOPS before performing an upgrade.

---

## v0.9.4

**When upgrading, make sure to install and run v0.9.3 prior to installing v0.9.4.**

### Changes
- removed dependency on Keycloak.
- UI Improvements.

### Upgrade

- Environment variable `KHEOPS_KEYCLOAK_URI` is no longer used.
- Environment variable `KHEOPS_KEYCLOAK_REALMS` is no longer used.
- Environment variable `KHEOPS_KEYCLOAK_CLIENTID` is no longer used.
- Docker secret `kheops_keycloak_clientsecret` is no longer used.
- Docker secret `kheops_metric_ressource_password` is no longer used.
  #### Note when removing secrets - don't forget to remove reverences to secrets in the *docker-compose.yml*.
- The Keycloak *kheopsAuthorization* Serivce Account is no longer used.

---

## v0.9.3

**Transition version to v0.9.4**

Versions of KHEOPS up to v0.9.2 had a strong dependecy on Keycloak. Only the user's ID (sub) was stored in the KHEOPS database, and KHEOPS would use Keycloak APIs and a service account (kheopsAuthorization) to discover a user's name and email address. 

 Starting with v0.9.4, KHEOPS uses exclusivly OpenID Connect (OIDC) for user authentication. v0.9.3 is a transtion release that is still dependent on Keycloak, and retrieves the user names and emails from Keycloak at startup.

### Changes
- Loads user profile information from Keycloak at startup
- Updates user profile information at each login.
- UI Improvements.

### Upgrade

- Add Environment variable `KHEOPS_OIDC_PROVIDER` which is a URL that points to the OpenIDConnect Provider.

  The full path to the OIDC provider must be set. Its value will typically be `${KHEOPS_KEYCLOAK_URI}/auth/realms/${KHEOPS_KEYCLOAK_REALMS}`
  
  For example: If the `KHEOPS_KEYCLOAK_URI` is "https://keycloak.kheops.online", and the `KHEOPS_KEYCLOAK_REALMS` is "demo", the value of `KHEOPS_OIDC_PROVIDER` would be "https://keycloak.kheops.online/auth/realms/demo".

- Environment variables `KHEOPS_ROOT_SCHEME`, `KHEOPS_ROOT_HOST`, and `KHEOPS_ROOT_PORT` are replaced by `KHEOPS_ROOT_URL`.

  The value of `KHEOPS_ROOT_URL` will typically be `${KHEOPS_ROOT_SCHEME}://${KHEOPS_ROOT_HOST}[:${KHEOPS_ROOT_PORT}]`. If the port is the default port for the scheme (e.g., 443 for https), it can be omitted.

- Environment variable `KHEOPS_UI_KEYCLOAK_CLIENTID` is renamed `KHEOPS_UI_CLIENTID`

- Make sure that the KHEOPS UI login client in Keycloak has the `web-origins`, `email`, and `profile` Client Scope as an Assigned Default Client Scope.


#### Notes for upgrading from nonsecure install

- Pass the internal docker network hostname of the Keycloak server as the `KHEOPS_OIDC_PROVIDER` to the *kheops-authorization* Docker container in the *docker-compose.yml* file.

  ``` yaml
      environment:
        KHEOPS_OIDC_PROVIDER: http://keycloak:8080/auth/realms/kheops
  ```
- Use the *osirixfoundation/kheops-authorization:v0.9.3-noissuer* image for the *kheops-authorization* container.


## Older versions

To upgrade from versions older than v0.9.2, please contact us directly at [support@kheops.online](mailto:support@kheops.online?subject=Upgrade%20KHEOPS) or [spalte@naturalimage.ch](mailto:spalte@naturalimage.ch?subject=Upgrade%20KHEOPS).
