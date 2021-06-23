# **AppFlexum API**

### In this article

- General Information
- Base URL
- Authorization
- Request Example
- Registration and Login
- Basic workflow
- User
- Automatically generated API Endpoints
- Error Codes

## GENERAL

Use AppFlexum API to build, integrate and configure your custom applications faster!

AppFlexum REST API allows you to create dynamic APIs and DataSets. When you need to quickly build your API, there are many things you need to do, before you can run your first call. Build any database structure with AppFlexum and let us do the rest. Create the tables, fields and views and allow AppFlexum backend to generate the corresponding endpoints automatically. Then you can operate with your data via the CRUD API endpoints, which were created automatically.

Main advantages:

- High-speed backend

- Horizontal scalability

- Quick access to your records regardless of the data volume (approx. 3 ms) 

- Data-driven Applications


## **BASE URL**

The Base URL is a service main URL that represents the network address of API. To work with AppFlexum API use the following service URL:

https://pub-122.api.v24.app

## **AUTHORIZATION**

Access to API is possible through the user token. The token is received in the response. 
The User has to send a username and password for the first time then the server returns an access-token in the response (see the following example for details). This token will be used instead of the username and password for further sessions. This token is valid for 3600 seconds*. 

**It will be configurable by the application settings in the future, also oAuth2 providers will be added (Google, Github, Twitter).*

The authorization token is passed through the request header, in the ‘X-Auth-Token’ parameter.

## **REQUEST EXAMPLE**

API calls have the following structure:

```curl
curl -H 'Content-type: application/json' -H 'X-Auth-Token: 12345' -X GET "https://fm-cp.api.v24.app/user/whoami",
```

**where**

- Content-type: application/json, type of content returned in the response (header parameter);
- X-Auth-Token, authorization token (header parameter);
- GET is an HTTP Method;
- https://fm-cp.api.v24.app/ is the base URL;
- user/whoami is a path to the requested resource (endpoint).

**NOTE**. All the request examples here have CURL format.

The endpoints represent various resources, which can be accessed (or changed) through the different HTTP methods (verbs): GET, POST, PUT, PATCH, and DELETE.

## REGISTRATION AND LOGIN

### USER REGISTRATION

To register a new user utilize the next call, provide your username, password, and password_confirm into the request body. 

**NOTE.** Registration request doesn't need an authorization token in the header.

***Registration call Example*** 

```curl
curl --location --request POST 'https://pub-122.api.v24.app/auth/register' \
--header 'Content-Type: application/json' \
--data-raw '{
    "username":"sj4_user",
    "password":"12345",
    "password_confirm":"12345"
}'
```

***Response body (200)*** 

```json
{
    "data": {
        "file_token": "ae679526-cf21-4b9c-85c5-1fdfe28242d6",
        "username": "sj4_user",
        "token": "4532f646-f0be-44f7-bf41-ea231f1148e0",
        "user_id": "6fb751af-0200-452a-901c-456ac02449b0"
    }
}
```

### **LOGIN**

To obtain an authorization token, the user must log in to the account.

***Required parameters***

| parameter | type   | description                                   |
| --------- | ------ | --------------------------------------------- |
| username  | string | User name, specified at the registration step |
| password  | string | Password, specified at the registration step  |

***Response Description***

| parameter  | type          | description                                                  |
| ---------- | ------------- | ------------------------------------------------------------ |
| file_token | string (uuid) | Token that protects user data. Files uploaded into the system have a permanent link that redirects to the actual file on the disk. To create this link, the API token is used. To have access to the file on the disk a file_token is used. This token provides access control to the file for the session. |
| token      | string (uuid) | An authorization token, generated to be used in the API calls header |
| username   | string        | A name of the user entered in the request body               |
| user_id    | string        | Id of the registered User. Users have an id (uuid) and also a unique friendly name (user_id). User can change this name at any time and utilize it to login with "alex" while it will be mapped to their user's uuid. User can change user_id in the future, as long as user_id is unique in the system. Currently, we don't keep the history of previously used user_ids. That's why user can utilize user_id that was just decommissioned by some other user. |

***Login Example***

```curl
curl --location --request POST 'https://pub-122.api.v24.app/auth/login' \
--header 'Content-Type: application/json' \
--data-raw '{
    "username":"sj1_admin",
    "password":"12345"
}'
```

***Response body (200)*** 

```json
{
    "data": {
        "file_token": "b4996317-880b-46ee-9c8c-a79cda88a8fe",
        "username": "sj1_admin",
        "token": "1a2e6a34-f41f-4575-a3fe-5895ebe1619c",
        "user_id": "8cb2048d-7633-476b-805c-629b2a0cdc84"
    }
}
```



### **LOGOUT**

***Logout Example***

```curl
curl --location --request GET 'https://pub-122.api.v24.app/auth/logout' \
--header 'X-Auth-Token: 4532f646-f0be-44f7-bf41-ea231f1148e0'
```

***Response body (200)*** 

```json
{
    "data": "Success"
}
```

### **WHOAMI**

In some cases we need to check which user is logged in at the moment. For that purpose it is possible to get the current user information by the following request.

***WhoamI Example***

```curl
curl --location --request GET 'https://pub-122.api.v24.app/user/whoami' \
--header 'X-Auth-Token: 8b518ac8-a2b1-4634-8154-e320f481537a'
```

***Response body (200)*** 

```json
{
    "data": {
        "active": true,
        "user_id": "6cd2b12f-23f8-47d3-828a-95a4b6083ab3",
        "username": "marta",
        "file_token": "ead3f8cc-b6d6-4d96-9c9b-27550862337d",
        "roles": {
            "6cd2b12f-23f8-47d3-828a-95a4b6083ab3": {
                "id": "6cd2b12f-23f8-47d3-828a-95a4b6083ab3"
            },
            "public": {
                "id": "public"
            },
            "aedcd15c-26c3-4a3d-8c20-818cb1e8f5e0": {
                "id": {
                    "aedcd15c-26c3-4a3d-8c20-818cb1e8f5e0": {
                        "id": "hr",
                        "name": "HR"
                    }
                }
            }
        }
    }
}
```

## BASIC WORKFLOW

Basic Workflow is the following:

0. Register a user
1. Login and receive a token
2. Create Roles (user and admin)
3. Create Object
4. Create Fields
5. Create View (default and admin views, access control type ACL, RBAC, ABAC)
6. Use generated API to create, edit, get and delete the records (POST/GET/PATCH/DELETE).

## USER

To start work you need to register a user. Please, see user registration section above.

### User Roles Hierarchy

Each object has a roles hierarchy and you can specify the parent's access to the indexes and data of its children. 

By default, the user has "auth" and "public" roles and then the roles defined for the user who creates a new record in the object. 

*For example, there is a user X with role 10. User X has a role in the object named Role10. The Parent role is ParentRole5. Then, when user X adds a new record to the object, ParentRole5 get this record into its index. So this ParentRole5 can list / view / edit the data (if it is allowed in its role).*

**Note.** ParentRole5 can have its parent (ParentRole1). ParentRole5 may be excluded from this chain but the ParentRole1 may be included. In this case, ParentRole5 does NOT have access to the Role10's data, but ParentRole1 does. Then users of ParentRole1 can list the records of Role10 and potentially edit and delete them based on the object controls and their views.

It's a hierarchical way of assigning more privileges (RBAC) which maintains ABAC via the views.

### **RBAC and ABAC** access control

Here are some basics on access control used in the project. 

**Role-based access control (RBAC)** is an access control method based on defining employee roles and corresponding privileges within the organization. The idea of this model is that every employee is assigned a role. Every role has a collection of permissions and restrictions. An employee can access objects and execute operations only if their role in the system has the relevant permissions.

**Hierarchical RBAC** implements a hierarchy within the role structure. This hierarchy establishes the relationships between roles. Users with senior roles acquire permissions of all junior roles, which are assigned to their subordinates. The complexity of the hierarchy is defined by the needs of the company.

**Attribute-based access control** **(ABAC)** is a model that evolved from RBAC. This model is based on establishing a set of attributes for any element of your system. A central policy defines which combinations of user and object attributes are required to perform any action.

Unlike in RBAC, in ABAC you can even use attributes that aren’t yet registered in the system but will appear during the work process.

Once you’ve created policies for the most common job positions and resources in your company, you can simply copy them for every new user and resource. This is similar to how a role works in the RBAC model, but in the ABAC model, attributes can be modified for the needs of a particular user without creating a new role.

In the project, the views give us a possibility to go by a hierarchical way of assigning more privileges (RBAC) which maintains ABAC.

Let's take a look at an example. Assume we need to create the following database table (see Table 1) that contains fields id (UUID), username, email, street, city, and country, and requires authentication. This table represents the fields of object 'employee_address' with the restrictions listed in Table 2.

**Table 1. Employee Address**

| uuid                             | userName   | email                | street      | city      | country      |
| -------------------------------- | ---------- | -------------------- | ----------- | --------- | ------------ |
| dynamically generated by backend | alex.smith | alex.smith@gmail.com | some street | some city | some country |

Two roles can access the object: admin & user. Admin has access to all things whereas user has access to create, view and edit methods. Admin can create unlimited records(-1) whereas the user can only create a maximum of 10 records.

**Table 2. Restrictions needed for Admin and User**

|       | view  | get list | edit  | create | delete | Max Records Q-ty |
| ----- | ----- | -------- | ----- | ------ | ------ | ---------------- |
| user  | **+** | –        | **+** | **+**  | –      | 10               |
| admin | **+** | **+**    | **+** | **+**  | **+**  | unlimited        |

Admin and User can both create (fill) fields 'username', 'email', 'street', 'city' and 'country'.

Admin can edit and view all fields whereas User can only edit fields 'username', 'street', 'city', and 'country', and only view fields 'street', 'city', and 'country'.

Admin can also create, edit and view all fields of the user.

### **Create Object**

***Hereinafter, the database table stands for object and create an object means create a table.*** (A glossary will be added if required)

***Parameters Description***

| parameter       | type    | description                                                  |
| --------------- | ------- | ------------------------------------------------------------ |
| id              | string  | Unique object id                                             |
| name            | string  | Name of the object. You can choose any name                  |
| description     | string  | Object Description                                           |
| meta            | object  | Metadata, see the description below                          |
| requires_auth   | boolean | Authentication                                               |
| roles_hierarchy | array   | Hierarchy of roles<br /><br />**can_view**: User can read a single item;<br />**can_list**: user can list all the items;<br />**can_delete**: user can delete an item;<br />**can_create**: user can create an item;<br />**can_edit**: user can edit an item;<br />**access:** master control. If false, then can_view, can_list, can_delete, can_create, can_edit will be false;<br />**own_idx** is a place where items created by the owner (an authenticated user) resides;<br />**role_idx** is a place where items created by the users of same roles reside (mix of all items created by users of the same role). Let's say there are two users "a" and "b" with the admin role. If user "a" creates a record then it will go to his `own_idx` and `role_idx`. If  user "b" creates a record then it will go to his `own_idx` (not the `own_idx` of user "a". The place is different). However, it will go the same `role_idx` though.<br /><br />These parameters specify what users can do to their own data (record added by themselves) or to data belonging to one of their roles (for example employees can view and edit their phone number own_index but employees can only view the phone number of other employees). A parent role (HR) can view and edit phone numbers of their children and can edit their salary field. Employees role owner of data can view the salary but cannot edit it. Employee role can not view the salary field (because then one employee would be able to view salary of others).<br /><br />**NOTE**: own_idx is enabled by default and cannot be disabled.<br />**own_idx.record_limit:** a number of records that this role can create.<br />**own_idx.enabled:** should build a list? If false then the list API will return empty. |
| parents         | array   | Role of Parent in the Object roles hierarchy.                |



**To create this table structure use the following API request.**

***Create Object Example***

```curl
curl --location --request POST 'https://pub-122.api.v24.app/object' \
--header 'X-Auth-Token: <auth_token>' \
--header 'Content-Type: application/json' \
--data-raw '{
    "id": "emp_addresses",
    "name": "Emp Addresses",
    "description": "Object for employee addresses",
    "meta": {
        "requires_auth": true,
        "roles_hierarchy": [
             {
                "_id": "aedcd15c-26c3-4a3d-8c20-818cb1e8f5e0" 
                "can_view": true,
                "can_list": true,
                "can_delete": true,
                "can_create": true,
                "can_edit": true,
                "access": true,
                "own_idx": {
                    "record_limit": -1
                },
                "role_idx": {
                    "record_limit": -1,
                    "enabled": true
                }
            },
            {
                "_id": "271bacc6-5d50-47f5-9205-1082f4fbbe4b"
                "can_view": true,
                "can_list": false,
                "can_delete": false,
                "can_create": true,
                "can_edit": true,
                "access": true,
                "own_idx": {
                    "record_limit": -1
                },
                "role_idx": {
                    "record_limit": -1,
                    "enabled": true
                },
                "hierarchy_idx": {
                    "enabled": true
                },
                "parents": ["aedcd15c-26c3-4a3d-8c20-818cb1e8f5e0"]
            }
        ]
    }
}'
```



***Response body***

Status code: 200

```json
{

  "data": "Success"

}
```



### Create Related Objects

If you need to create two or more related objects (tables) use the 'lookup' field. For more information see the example below. 

Let's create 'Employee' and 'Employee_addresses' objects.

There is a special type of field which is called "lookup". It allows us to map another object as a "subquery" of the original object:

```
Object Employee
    Name: string
    Surname: string
    Primary Address: lookup Address / owner allowed 1 record entry
    Other Addresses: lookup Address / owner allowed 10 record entries
```

and then you have

```
Object Address
    StreetName: string
    StreetNumber: string
    PO_Box: string
    ...
```

The object 'Employee' has the local entries (name and surname) and 2 lookup fields of object_type 'address'. The primary address allows adding only 1 record, while other addresses allow up to 10 for the user.



### **Create fields**

When we've created the Object, we can add its fields. All the fields in our table must be a string and must have a minimum length of 2 and a maximum length of 255. An `email` should have validation of email, and username must be a primary key.

***Parameters Description***

| parameter        | type          | description                                                  |
| ---------------- | ------------- | ------------------------------------------------------------ |
| id               | string        | Unique identifier. For example, you get an API response of something like `{"username":"alex.smith"}`. Here username is the field id. |
| name             | string        | Any user name                                                |
| description      | string        | Any description you want.                                    |
| type             | string (Enum) | Type of field. Currently there are 4 supported types: string, number, Boolean and file. |
| settings         | object        | The field settings. <br />**index:** Primary key. That also means it is unique in every object. |
| validation_rules | object        | **minlen:2**:** minimum length 2 symbols<br />**maxlen:255:** maximum length 255 symbols<br />**email:** Boolean (if true, email validation will be performed) |



***Create Fields Example***

```curl
curl --location --request POST 'https://pub-122.api.v24.app/object/employee_addresses/properties' \
--header 'X-Auth-Token: <auth_token>' \
--header 'Content-Type: application/json' \
--data-raw '[
    {
        "id": "username",
        "name": "Username",
        "description": "Username",
        "type": "string",
        "settings": {
            "index": "primary",
            "validation_rules": {
                "minlen:2": true,
                "maxlen:255": true
            }
        }
    },
    {
        "id": "email",
        "name": "Emailsupported 
        "description": "Email",
        "type": "string",
        "settings": {
            "validation_rules": {
                "email": true,
                "minlen:2": true,
                "maxlen:255": true
            }
        }
    },
    {
        "id": "street",
        "name": "Street",
        "description": "Street",
        "type": "string",
        "settings": {
            "validation_rules": {
                "minlen:2": true,
                "maxlen:255": true
            }
        }
    },
    {
        "id": "city",
        "name": "City",
        "description": "City",
        "type": "string",
        "settings": {
            "validation_rules": {
                "minlen:2": true,
                "maxlen:255": true
            }
        }
    },
    {
        "id": "country",
        "name": "Country",
        "description": "Country",
        "type": "string",
        "settings": {
            "validation_rules": {
                "minlen:2": true,
                "maxlen:255": true
            }
        }
    }
]'
```



***Response body***

Status code: 200

```json
{

  "data": "Success"

}
```

This response means that we've successfully created and added the fields to our table.



### **Create views**

Defines what fields the roles has access to. There are 3 View Types: default, admin and user.

The permissions we define in default, admin and user view will be merged. For example in one view has `{"create": true}` and other view has `{"create": false}` then `{"create": true}` will be used. 

**Default View**

When you create an object the default view is already created with no permissions. That's why we need to utilize PATCH (update the Default View with fields) only for the default view.

Admin and user can create fields - `username`, `email`, `street`, `city` and `country` so we will create a default view for default role in this case.

**Key terms**

| View   | Description                                                  |
| ------ | ------------------------------------------------------------ |
| owner  | The user who creates the record                              |
| role   | The user belonging to the specified role                     |
| other  | Specified role's children. User role is a child of admin role. In this case, admin has access to create, edit or view fields of user role. |
| auth   | Every authenticated user.                                    |
| public | Every non authenticated user.                                |

**Parameters Description**

| parameter  | type   | description                                                |
| ---------- | ------ | ---------------------------------------------------------- |
| _id        | string | ID of the View                                             |
| role_id    | string | ID of the role that the view is created for                |
| name       | string | Name of the created view                                   |
| properties | array  | The list of fields, the access to which must be configured |

***Create Default View Example***

```curl
curl --location --request PATCH 'https://pub-122.api.v24.app/object/emp_addresses/views' \
--header 'X-Auth-Token: 8dd4b80b-b209-4da5-bd28-9d5eaa24b65a' \
--header 'Content-Type: application/json' \
--data-raw '[{

"_id":"default",
"role_id": "e0090161-a767-4c14-86c9-82f9bf011ea3",

    "name": "Default view",
    "properties": [
    
    {
        "_id": "e5c540c6-7e15-455a-894b-c8f957c35eb1",
            "owner": {
                "create": true
            },
        "_id": "e5c540c6-7e15-455a-894b-c8f957c35c35",
            "owner": {
                "create": true
            },
        "_id": "e5c540c6-7e15-455a-894b-c8f957cc8f92",
            "owner": {
                "create": true
            },
        "_id": "e5c540c6-7e15-455a-894b-c8f957ccc8f9",
            "owner": {
                "create": true
            },
        "_id": "e5c540c6-7e15-455a-894b-c8f957ce5c54",
            "owner": {
                "create": true
            }
    ]
}]'
```

***Response body***

Status code: 200

```json
{
    "data": "Success"
}
```

**Admin View**

We will create a view for the admin where admin has access to view and edit fields - `username`, `email`, `street`, `city` and `country`. Admin also has access to view, create and edit all fields of the user.

**Note.** We didn't define `create: true` in admin view because it was already defined in the default view.

***Create Admin View Example***

**NOTE.**  In POST request, `_id` can only have `default` as its value.

```curl
curl --location --request POST 'https://pub-122.api.v24.app/object/emp_addresses/views' \
--header 'X-Auth-Token: <auth_token>' \
--header 'Content-Type: application/json' \
--data-raw '[{

"_id":"default",
"role_id": "e0090161-a767-4c14-86c9-82f9bf011ea3",

    "name": "Admin view",
    "properties": [
    
    {
        "_id": "e5c540c6-7e15-455a-894b-c8f957c35eb1",
            "owner": {
                "create": true
            },
        "_id": "e5c540c6-7e15-455a-894b-c8f957c35c35",
            "owner": {
                "create": true
            },
        "_id": "e5c540c6-7e15-455a-894b-c8f957cc8f92",
            "owner": {
                "create": true
            },
        "_id": "e5c540c6-7e15-455a-894b-c8f957ccc8f9",
            "owner": {
                "create": true
            },
        "_id": "e5c540c6-7e15-455a-894b-c8f957ce5c54",
            "owner": {
                "create": true
            }
    ]
}]'
```

***Response body***

Status code: 200

```json
{
    "data": "Success"
}
```



**User View**

We will create a view for the user where user can edit `username`, `street`, `city`, `country` and view `street`, `city` and `country`.

**Note.** For example in this case, username can't be viewed. So you can just make it false `{"view": false}` or not define it at all. By default, it will be false.

***Create User View Example***

```curl
curl --location --request POST 'https://pub-122.api.v24.app/object/emp_addresses/views' \
--header 'X-Auth-Token: <auth_token>' \
--header 'Content-Type: application/json' \
--data-raw '[{
    "name":"User view",
    "role_id": "e0090161-a767-4c14-86c9-82f9bf011ea3",
    "_id":"bd6ad0fa-2264-4996-901c-ed840f539465",

    "name": "User view",
    "properties": [
    
    {
        "_id": "e5c540c6-7e15-455a-894b-c8f957c35eb1",
            "owner": {
                "create": true
            },
        "_id": "e5c540c6-7e15-455a-894b-c8f957c35c35",
            "owner": {
                "create": true
            },
        "_id": "e5c540c6-7e15-455a-894b-c8f957cc8f92",
            "owner": {
                "create": true
            },
        "_id": "e5c540c6-7e15-455a-894b-c8f957ccc8f9",
            "owner": {
                "create": true
            },
        "_id": "e5c540c6-7e15-455a-894b-c8f957ce5c54",
            "owner": {
                "create": true
            }
    ]
}]'
```

***Response body***

Status code: 200

```json
{
    "data": "Success"
}
```



## **AUTOMATICALLY GENERATED API ENDPOINTS**

When you create an object, the following APIs are generated automatically.

```
/objects/:objectId?page=:pageNumber,:per_page=:perPage,sorting=:sorting(oldest,newest,updated), -X GET (LIST)
/objects/:objectId -X POST (CREATE)
/objects/:objectId/:recordId -X GET (READ)
/objects/:objectId/:recordId -X PATCH (EDIT)
/objects/:objectId/:recordId -X DELETE (DELETE)
/objects/:objectId/bulk/delete?id=:id1,:id2 -X DELETE (BULK DELETE)
```

Where  `:recordId` is the primary key item id. So, if username is `alex.smith` then you can call `/objects/emp_addresses/alex.smith`. If you want to get data by uuid you can call `/data/:uuid`. The  `:uuid` parameter is an  `_id` in API response. 

Note that you cannot get data using uuid in the request like `/objects/emp_addresses/:uuid` .



To add new Employee Address into the object use the request below.

***Add new Employee Address Example***

```curl
curl --location --request POST 'https://pub-122.api.v24.app/objects/emp_addresses' \
--header 'X-Auth-Token: <auth_token>' \
--header 'Content-Type: application/json' \
--data-raw '{
"email":"test@com.com",
"city": "Paris",
"street": "JeanJaqueStreet",
"username": "Jack.Daniels",
"country": "France"
}'
```

***Response body***

Status code: 200

```json
{
    "data": {
        "_id": "3d6099ac-3443-4c87-af3b-b6a60e0f7325",
        "_created_at": "1601473876410",
        "_updated_at": "1601473876410"
    }
}
```



***Get the Employee Address by User Name Example***

```curl
curl --location --request GET 'https://pub-122.api.v24.app/objects/emp_addresses/daniel_jackson' \
--header 'X-Auth-Token: <auth_token>'
```

***Response body***

Status code: 200

```json
{
    "data": {
        "_id": "8fe34315-45c6-4561-bbe5-fa35fdcbcd36",
        "_created_at": "1601475707639",
        "username": "daniel_jackson",
        "email": "test@com.com",
        "_owner": "8cb2048d-7633-476b-805c-629b2a0cdc84",
        "_updated_at": "1601475707639",
        "country": "France",
        "street": "JeanJaqueStreet",
        "_creator": "8cb2048d-7633-476b-805c-629b2a0cdc84",
        "city": "Paris"
    }
}
```

***Get the List of Employee Addresses Example***

```curl
curl --location --request GET 'https://pub-122.api.v24.app/objects/emp_addresses' \
--header 'X-Auth-Token: <auth_token>'
```

***Response body***

Status code: 200

```json
{
    "data": [
        {
            "_id": "f95ab229-8e99-4679-ab5e-0372d097c064",
            "_created_at": 1601457238848,
            "username": "puere_cardin",
            "email": "test@test.com",
            "_updated_at": 1601457238848,
            "_creator": "8cb2048d-7633-476b-805c-629b2a0cdc84",
            "country": "France",
            "street": "bestStreet",
            "_owner": "8cb2048d-7633-476b-805c-629b2a0cdc84",
            "city": "Paris"
        },
        {
            "_id": "3d6099ac-3443-4c87-af3b-b6a60e0f7325",
            "_created_at": 1601473876410,
            "username": "jeanjaque",
            "email": "test@com.com",
            "_updated_at": 1601473876410,
            "_creator": "8cb2048d-7633-476b-805c-629b2a0cdc84",
            "country": "France",
            "street": "JeanJaqueStreet",
            "_owner": "8cb2048d-7633-476b-805c-629b2a0cdc84",
            "city": "Paris"
        },
        {
            "_id": "cd08d1f1-3ced-498c-af13-bef9dc9fb6f0",
            "_created_at": 1601475672209,
            "username": "jack.daniels",
            "email": "test@com.com",
            "_updated_at": 1601475672209,
            "_creator": "8cb2048d-7633-476b-805c-629b2a0cdc84",
            "country": "France",
            "street": "JeanJaqueStreet",
            "_owner": "8cb2048d-7633-476b-805c-629b2a0cdc84",
            "city": "Paris"
        },
        {
            "_id": "8fe34315-45c6-4561-bbe5-fa35fdcbcd36",
            "_created_at": 1601475707639,
            "username": "daniel_jackson",
            "email": "test@com.com",
            "_updated_at": 1601475707639,
            "_creator": "8cb2048d-7633-476b-805c-629b2a0cdc84",
            "country": "France",
            "street": "JeanJaqueStreet",
            "_owner": "8cb2048d-7633-476b-805c-629b2a0cdc84",
            "city": "Paris"
        }
    ],
    "meta": {
        "page": 1,
        "total": 4,
        "per_page": 10
    }
}
```



***Change the Employee Address by User Name***

```curl
curl --location --request PATCH 'https://pub-122.api.v24.app/objects/emp_addresses/Daniel_Jackson' \
--header 'X-Auth-Token: <auth_token>' \
--header 'Content-Type: application/json' \
--data-raw '{
"email":"update@update.com"
}'
```

***Response body***

Status code: 200

```json
{
    "data": {
        "_id": "8fe34315-45c6-4561-bbe5-fa35fdcbcd36",
        "_updated_at": "1601476161460",
        "email": "update@update.com"
    }
}
```



***Delete the Employee Address by Id Example***

```curl
curl --location --request DELETE 'https://pub-122.api.v24.app/objects/emp_addresses/3d6099ac-3443-4c87-af3b-b6a60e0f7325' \
--header 'X-Auth-Token: <auth_token>' \
--header 'Content-Type: application/json' \
}'
```

***Response body***

Status code: 200

```
Succsess Message
```



***Bulk Delete the Employee Addresses by the list of Ids***

```curl
curl --location --request DELETE 'https://pub-122.api.v24.app/objects/emp_addresses/bulk/delete?id=3d6099ac-3443-4c87-af3b-b6a60e0f7325, 3d6099ac-3443-4c87-af3b-b6a60e0f7325' \
--header 'X-Auth-Token: <auth_token>' \
--header 'Content-Type: application/json' \
}'
```

***Response body***

Status code: 200

```
Succsess Message
```



## ERROR CODES

| Issue                         | Error Message                 | Description                                           |
| ----------------------------- | ----------------------------- | ----------------------------------------------------- |
| Authorization failure         | Not authenticated             | User is logged out                                    |
|                               | No token specified            | User didn't provide API key in the header of request  |
| Wrong HTTP method in the call | 404 Not Found                 | Wrong method used, GET used instead of POST, etc.     |
| Can't create a record         | Record already exists         | You are trying to create a record that already exists |
| Field missed                  | This field is required        | Some required fields are missed                       |
| Validation rule doesn't exist | Validation rule doesn't exist | Validation rule must be added                         |
| Role not found                | Role not found                | The role doesn't exist                                |
|                               |                               |                                                       |




