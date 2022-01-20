# Description
PoC keycloak with a client configured resource permissions

## Configuration
To create a client that control permissions we must to follow these steps:

- Create a realm with namelled **poc**
Create a realm to group all security entities: client, roles and permissions

![Create a realm](captures/create_realm.png "Create a realm")

- Create a client with cliend id: **backend-permission**
We must create a client of type confidential and active the Authorization flag lñike this

![Create a client](captures/create_client.png "Create a client")

- Create the roles
Now we must create three roles for the realm with these names:

  **admin**
  **operator**
  **user**

![Create the roles](captures/create_roles.png "Create the roles")

- Create the users

Now we must create three users attached to these roles with this usernames and password test

  **admin** user with admin role attached
  **operator** user with opererator role attached
  **user** with user role attached

![Create the users](captures/create_users.png "Create the users")

- Define the permissions table

Now we must create the UMA entities to authorize our endpoints using permissions: scopes, resources, policies and permissions entities

The permission table that we configured in the client will be:

| Scope                      | Resource      | Policy          | 
|----------------------------|:-------------:|----------------:|
| read, create, edit, delete | client        | Admin Policy    | 
| read, edit                 | client        | Operator Policy |
| read                       | client        | User Policy     |

- Create the scopes

First create the scope for all resources. We list all scopes and the verbs related with them:

  **view**: capacity list resources (GET)
  **create**: capacity to insert resources (POST)
  **edit**: capacity to update resources (PUT)
  **delete**: capacity to delete resources (DELETE)

![Create scopes](captures/create_scopes.png "Create scopes")

- Create the resources

Second we create a resource called client that represent the methos /clients/* endpoint. We must attached all scopes previously configured to this resource


![Create resource](captures/create_resource.png "Create resource")

- Create the policies

Third we creae three policies on for each role created previosuly. These policies will be:

  **admin**: Admin Policity attached to Admin Role
  **operator**: Operator Policity attached to Operator Role
  **user**: User Policity attached to User Role

![Create policies](captures/create_policies.png "Create policies")
  
- Create the permissions

Finally we can create four permissions attached to the resource and each scope previously created. All of these permission must follow a Affirmative Decision Strategy. These permissions are:

  **client-view**: Permission atatched to client resource for scope view
  **client-create**: Permission atatched to client resource for scope create
  **client-edit**: Permission atatched to client resource for scope edit
  **client-delete**: Permission atatched to client resource for scope delete

![Create permission](captures/create_permission.png "Create permission")

![Create permission sample](captures/create_permission_sample.png "Create permission sample")

- Now we could validate all of this configuration using a keycloak tool called **evaluate**

--> For example we could check all scoped permied for a **admin actor**:

![Admin scope validated](captures/evaluate_admin_scopes_.png "Admin scope validated")

The result it's:

![Admin scope validated results](captures/evaluate_admin_scopes_results.png.png "Admin scope validated results")

--> For example we could check all scoped permied for a **operator actor**:

![Operator scope validated](captures/evaluate_operator_scopes.png "Operator scope validated")

The result it's:

![Operator scope validated results](captures/evaluate_operator_scopes_results.png "Operator scope validated results")

--> For example we could check all scoped permied for a **user actor**:

![User scope validated](captures/evaluate_user_scopes.png "User scope validated")

The result it's:

![User scope validated results](captures/evaluate_user_scopes_results.png "User scope validated results")

## Debug

Before validate the client resources from keycloak we are going to debug our client using Postman

-> First we login with Admin user

```shell
http://localhost:8080/auth/realms/poc/protocol/openid-connect/token

Content-Type: application/x-www-form-urlencoded
client_id: backend-permission
client_secret: 28nXT8EyAfH7h66YS3fOCsLhojfY4x6N
grant_type: password
username: admin
password: admin
```

![Login admin user](captures/postman_login_admin_user.png "Login admin user")

-> Second we evaluate the create scope for example


```shell
http://localhost:8080/auth/realms/poc/protocol/openid-connect/token

Bearer Token: Access Token obtained in the previous step

Content-Type: application/x-www-form-urlencoded
audience: backend-permission (the same as client_id)
grant_type: urn:ietf:params:oauth:grant-type:uma-ticket
permission: client-resource#create (RESOURCE_NAME#SCOPE_NAME)
response_mode: decision (reseume reponse)
```

![User scope validated results](captures/postman_validate_admin_user.png "Evaluate User scopes")

-> If Login with user actor and obtain a new access token and check again the same resource/scope result we obtain this result

![User scope validated results](captures/postman_validate_user_user.png "Evaluate User scopes")

Other request is recover all permissions of the operator user for example

```shell
http://localhost:8080/auth/realms/poc/protocol/openid-connect/token

Content-Type: application/x-www-form-urlencoded
audience: backend-permission (the same as client_id)
grant_type: urn:ietf:params:oauth:grant-type:uma-ticket
```

We obtain a UMA-Token and JWT with this information

![Permission operator user List](captures/uma_token_get_resources.png "Permission operator user List")

# Exports
- We could impot the keycloak realm
- We could import the REST Postman Endpoints