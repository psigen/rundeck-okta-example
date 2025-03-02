# Rundeck

This is a working example of integrating Rundeck with Okta SSO. It uses
vouch-proxy and nginx to create an authentication shim that passes the logged in
user and roles to Rundeck via its preauthenticated system.

1.  In Okta, create an **OIDC** application as per
    [this guide](#setting-up-an-okta-app).

2.  Create an `.env` file with the following content:

    ```bash
    OKTA_CLIENT_ID=<okta_client_id>
    OKTA_CLIENT_SECRET=<okta_client_secret>
    OKTA_DOMAIN=<okta_domain>.okta.com
    ```

3.  Start the local server using either `docker-compose` or `tilt`:

    ```bash
    docker compose up -d
    # -- or --
    tilt up
    ```

## Setting up an Okta app

1. Log into the Admin Console.

2. Browse to `Applications` and select `Create App Integration`.

3. Select `OIDC - OpenID Connect` as the **Sign-in method**, and
   `Web Application` as the **Application type**

4. On the **New Web App Integration** page:

   - Fill in anything you like for the **App integration name** and **Logo**
   - For **Sign-in redirect URIs** add a single entry of
     `http://localhost:8080/vouch/auth`
   - For **Sign-out redirect URIs** add a single entry of
     `http://localhost:8080/user/login`
   - Leave **Trusted Origins - Base URIs** blank.
   - For **Assignments - Controlled access**, choose
     `Skip group assignment for now`, and manually add users or groups to the
     app later.
   - Click `Create` to generate the app.

5. On the **Applications - [App Name]** page:

   - Copy the **Client Credentials - Client ID** for use in the `.env` file.
   - Copy the **Client Secrets - Secret** for use in the `.env` file.
   - Click on the **Sign On** tab, then click the **Settings - Sign on methods**
     `Configure profile mapping` link.
   - Click `[x]` to close the **[App Name] - User Profile Mappings** page.

6. On the **Profile Editor** page:

   - Click on the `+ Add Attibute` button.
   - Fill in the form with the following values:

     - **Data type**: `string`
     - **Display name**: `Roles`
     - **Variable name**: `roles`
     - **Description**: `Roles assigned to user in Rundeck`
     - **Enum**: `[x]` _(checked)_
     - **Attribute Members**:
       - **Display name**: `Admin`, **Value**: `admin`
       - **Display name**: `Ops Admin`, **Value**: `ops_admin`
       - **Display name**: `App Admin`, **Value**: `app_admin`
       - **Display name**: `User`, **Value**: `user`
     - **Attribute required**: `[x]` _checked_
     - **Attribute type**: `Group`

   - Click the `Save` button.

## Errata

- For some reason, when a role assignment is changed in Okta, on the next
  signout/signin cycle, Rundeck will store _both_ the old and new roles. A
  subsequent signout/signin cycle will only have the new role.

## References

- https://developer.okta.com/blog/2018/08/28/nginx-auth-request
- https://docs.rundeck.com/docs/administration/security/authentication.html#preauthenticated-mode-using-headers
- https://github.com/vouch/vouch-proxy?tab=readme-ov-file#vouch-proxy-in-a-path
- https://github.com/rundeck/rundeck/issues/5656
