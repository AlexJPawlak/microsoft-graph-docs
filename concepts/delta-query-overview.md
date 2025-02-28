---
title: "Use delta query to track changes in Microsoft Graph data"
description: "Delta query enables applications to discover newly created, updated, or deleted entities without performing a full read of the target resource with every request. Microsoft Graph applications can use delta query to efficiently synchronize changes with a local data store."
author: "davidmu1"
ms.localizationpriority: high
ms.custom: graphiamtop20
---

# Use delta query to track changes in Microsoft Graph data

Delta query enables applications to discover newly created, updated, or deleted entities without performing a full read of the target resource with every request. Microsoft Graph applications can use delta query to efficiently synchronize changes with a local data store.

> [!div class="nextstepaction"]
> [Tutorial: Use Change Notifications and Track Changes with Microsoft Graph](/learn/modules/msgraph-changenotifications-trackchanges)

## Use delta query to track changes in a resource collection

The typical call pattern is as follows:

1. The application begins by calling a GET request with the delta function on the desired resource.
2. Microsoft Graph sends a response containing the requested resource and a [state token](#state-tokens).

     a.  If a `nextLink` URL is returned, there may be additional pages of data to be retrieved in the session. The application continues making requests using the `nextLink` URL to retrieve all pages of data until a `deltaLink` URL is returned in the response.

     b.  If a `deltaLink` URL is returned, there is no more data about the existing state of the resource to be returned. For future requests, the application uses the `deltaLink` URL to learn about changes to the resource.

3. When the application needs to learn about changes to the resource, it makes a new request using the `deltaLink` URL received in step 2. This request *may* be made immediately after completing step 2 or when the application checks for changes.
4. Microsoft Graph returns a response describing changes to the resource since the previous request, and either a `nextLink` URL or a `deltaLink` URL.

>**Note:** Resources stored in Azure Active Directory (such as users and groups) support "sync from now" scenarios. This allows you to skip steps 1 and 2 (if you're not interested in retrieving the full state of the resource) and ask for the latest `deltaLink` instead. Append `$deltaToken=latest` to the `delta` function and the response will contain a `deltaLink` and no resource data. Resources in OneDrive and SharePoint also support this feature. For resources in OneDrive and SharePoint, append `token=latest` instead.

>**Note:** The delta query function is generally referred to by appending `/delta` to the resource name. However, `/delta` is a shortcut for the fully qualified name `/microsoft.graph.delta` that you see in requests generated by the Microsoft Graph SDKs.

>**Note:** The initial request to the delta query function (no delta or skip token) will return the resources that currently exist in the collection. Resources that have been created and deleted prior to the initial delta query won't be returned. Updates made before the initial request are summarized on the resource returned as its latest state.

### State tokens

A delta query GET response always includes a URL specified in a `nextLink` or `deltaLink` response header.
The `nextLink` URL includes a _skipToken_, and a `deltaLink` URL includes a _deltaToken_.

These tokens are opaque to the client. The following details are what you need to know about them:

- Each token reflects the state and represents a snapshot of the resource in that round of change tracking.

- The state tokens also encode and include other query parameters (such as `$select`) specified in the initial delta query request. Therefore, it's not required to repeat them in subsequent delta query requests.

- When carrying out delta query, you can copy and apply the `nextLink` or `deltaLink` URL to the next **delta** function call without having to inspect the contents of the URL, including its state token.

### Optional query parameters

If a client uses a query parameter, it must be specified in the initial request. Microsoft Graph automatically encodes the specified parameter into the `nextLink` or `deltaLink` provided in the response. The calling application only needs to specify the query parameters once upfront. Microsoft Graph adds the specified parameters automatically for all subsequent requests.

Note the general limited support of the following optional query parameters:

- `$orderby`

    Do not assume a specific sequence of the responses returned from a delta query. Assume that the same item can show up anywhere in the `nextLink` sequence and handle that in your merge logic.
- `$top`

    The number of objects in each page can vary depending on the resource type and the type of changes made to the resource.

For the [message](/graph/api/resources/message?view=graph-rest-1.0) resource, see details for [query parameters support in a delta query](delta-query-messages.md#use-query-parameters-in-a-delta-query-for-messages).

For the [user](/graph/api/resources/user?view=graph-rest-1.0) and [group](/graph/api/resources/group?view=graph-rest-1.0) resources, there are restrictions on using some query parameters:

- `$expand` is not supported.
- `$top` is not supported.
- `$orderby` is not supported.
- If a `$select` query parameter is used, the parameter indicates that the client prefers to only track changes on the properties or relationships specified in the `$select` statement. If a change occurs to a property that is not selected, the resource for which that property changed does not appear in the delta response after a subsequent request.
- `$select` also supports `manager` and `members` navigational property for users and groups respectively. Selecting those properties allows tracking of changes to user's manager and group memberships.

- Scoping filters allow you to track changes to one or more specific users or groups by object ID. For example, the following request returns changes for the groups matching the IDs specified in the query filter.

<!-- {
  "blockType": "request",
  "name": "group_delta"
}-->
```http
https://graph.microsoft.com/beta/groups/delta/?$filter=id eq '477e9fc6-5de7-4406-bb2a-7e5c83c9ae5f' or id eq '004d6a07-fe70-4b92-add5-e6e37b8acd8e'
```

## Resource representation in the delta query response

- Newly created instances of a supported resource are represented in the delta query response using their standard representation.

- Updated instances are represented by their **id** with *at least* the properties that have been updated, but additional properties may be included.

- Relationships on users and groups are represented as annotations on the standard resource representation. These annotations use the format `propertyName@delta`. The annotations are included in the response of the initial delta query request.

Removed instances are represented by their **id** and an `@removed` object. The `@removed` object may include additional information about why the instance was removed. For example,  "@removed": {"reason": "changed"}.

Possible @removed reasons can be *changed* or *deleted*.

- *Changed* indicates the item was deleted and can be restored from [deletedItems](/graph/api/resources/directory).

- *Deleted* indicates the item is deleted and cannot be restored.

The `@removed` object can be returned in the initial delta query response and in tracked (deltaLink) responses. Clients using delta query requests should be designed to handle these objects in the responses.

>**Note:** It's possible that a single entity will be contained multiple times in the response, if that entity was changed multiple times and under certain conditions. Delta queries enable your application to list all the changes, but can't ensure that entities are unified in a single response.

## Supported resources

Delta query is currently supported for the following resources. Note that some resources which are available in v1.0 have their corresponding **delta** functions still in preview status, as indicated.

| **Resource collection**                                        | **API**                                                                                                                                                      |
| :------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Applications                                                   | [delta](/graph/api/application-delta) function of the [application](/graph/api/resources/application) resource                                               |
| Administrative units (preview)                                 | [delta](/graph/api/administrativeunit-delta) function (preview) of the [administrativeUnit](/graph/api/resources/administrativeunit) resource                |
| Chat messages in a channel                                     | [delta](/graph/api/chatmessage-delta) function (preview) of the [chatMessage](/graph/api/resources/chatmessage)                                              |
| Directory objects                                              | [delta](/graph/api/directoryobject-delta) function (preview) of the [directoryObject](/graph/api/resources/directoryobject) resource                         |
| Directory roles                                                | [delta](/graph/api/directoryrole-delta?view=graph-rest-1.0) function of the [directoryRole](/graph/api/resources/directoryrole?view=graph-rest-1.0) resource |
| Drive items\*                                                  | [delta](/graph/api/driveitem-delta?view=graph-rest-1.0) function of the [driveItem](/graph/api/resources/driveitem?view=graph-rest-1.0) resource             |
| Education Classes                                              | [delta](/graph/api/educationclass-delta) function of the [educationClass](/graph/api/resources/educationclass) resource                                      |
| Education Users                                                | [delta](/graph/api/educationuser-delta) function of the [educationUser](/graph/api/resources/educationuser) resource                                         |
| Education Schools                                              | [delta](/graph/api/educationschool-delta) function of the [educationSchool](/graph/api/resources/educationschool) resource                                   |
| Events in a calendar view (date range) of the primary calendar | [delta](/graph/api/event-delta?view=graph-rest-1.0) function of the [event](/graph/api/resources/event?view=graph-rest-1.0) resource                         |
| Groups                                                         | [delta](/graph/api/group-delta?view=graph-rest-1.0) function of the [group](/graph/api/resources/group?view=graph-rest-1.0) resource                         |
| Mail folders                                                   | [delta](/graph/api/mailfolder-delta?view=graph-rest-1.0) function of the [mailFolder](/graph/api/resources/mailfolder?view=graph-rest-1.0) resource          |
| Messages in a folder                                           | [delta](/graph/api/message-delta?view=graph-rest-1.0) function of the [message](/graph/api/resources/message?view=graph-rest-1.0) resource                   |
| Organizational contacts                                        | [delta](/graph/api/orgcontact-delta?view=graph-rest-1.0) function of the [orgContact](/graph/api/resources/orgcontact?view=graph-rest-1.0) resource          |
| OAuth2PermissionGrants                                         | [delta](/graph/api/oauth2permissiongrant-delta) function of the [oauth2permissiongrant](/graph/api/resources/oauth2permissiongrant) resource                 |
| Personal contact folders                                       | [delta](/graph/api/contactfolder-delta?view=graph-rest-1.0) function of the [contactFolder](/graph/api/resources/contactfolder?view=graph-rest-1.0) resource |
| Personal contacts in a folder                                  | [delta](/graph/api/contact-delta?view=graph-rest-1.0) function of the [contact](/graph/api/resources/contact?view=graph-rest-1.0) resource                   |
| Planner items\*\* (preview)                                    | [delta](/graph/api/planneruser-list-delta) function (preview) of the all segment of [plannerUser](/graph/api/resources/planneruser) resource                 |
| Service principals                                             | [delta](/graph/api/serviceprincipal-delta) function of the [servicePrincipal](/graph/api/resources/serviceprincipal) resource                                |
| Tasks in a task list                                           | [delta](/graph/api/todotask-delta) function of the [todoTask](/graph/api/resources/todotask) resource                                                        |
| Task lists                                                     | [delta](/graph/api/todotasklist-delta) function of the [todoTaskList](/graph/api/resources/todotasklist) resource                                            |
| Users                                                          | [delta](/graph/api/user-delta?view=graph-rest-1.0) function of the [user](/graph/api/resources/user?view=graph-rest-1.0) resource                            |


> \* The usage pattern for OneDrive resources is similar to the other supported resources with some minor syntax differences. Delta query for drives will be updated in the future to be consistent with other resource types. For more detail about the current syntax, see
[Track changes for a drive](/graph/api/driveitem-delta?view=graph-rest-1.0).

> \*\* The usage pattern for Planner resources is similar to other supported resources with a few differences.  For details, see [Track changes for Planner](/graph/api/planneruser-list-delta).

## Limitations

### Properties stored outside of the main data store

Some resources contain properties that are stored outside of the main data store for the resource (for example, the user resource is mostly stored in the Azure AD system, while some properties, like **skills**, are stored in SharePoint Online). Currently, those properties are not supported as part of change tracking; a change to one of those properties will not result in an object showing up in the delta query response. Currently, only the properties stored in the main data store trigger changes in the delta query.

To verify that a property can be used in delta query, try to perform a regular `GET` operation on the resource collection, and select the property you're interested in. For example, you can try the **skills** property on the users collection.

```msgraph-interactive
GET https://graph.microsoft.com/v1.0/users/?$select=skills
```

Because the **skills** property is stored outside of Azure AD, the following is the response.

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.user",
  "isCollection": true
} -->

```http
HTTP/1.1 501 Not Implemented
Content-type: application/json

{
    "error": {
        "code": "NotImplemented",
        "message": "This operation target is not yet supported.",
        "innerError": {
            "request-id": "...",
            "date": "2019-09-20T21:47:50"
        }
    }
}
```

This tells you that the **skills** property is not supported for delta query on the **user** resource.

### Navigation properties

Navigation properties are not supported. For example, you cannot track changes to the users collection that would include changes to their **photo** property; **photo** is a navigation property stored outside of the user entity, and changes to it do not cause the user object to be included in the delta response.

### Processing delays

Expect varying delays between the time a change is made to a resource instance, which can be through an app interface or API, and the time the tracked change is reflected in a delta query response.

### National clouds

Delta queries are available for customers hosted on the public cloud and Microsoft Graph China operated by 21Vianet only.

### Replays

Your application must be prepared for replays, which occur when the same change appears in subsequent responses. While delta query makes a best effort to reduce replays, they are still possible.

### Synchronization reset

Delta query can return a response code of `410 (gone)` and a **Location** header containing a request URL with an empty delta token (same as the initial query). This is an indication that the application must restart with a full synchronization of the target tenant. This usually happens to prevent data inconsistency due to internal maintenance or migration of the target tenant.

### Token duration

Delta tokens are only valid for a specific period before the client application needs to run a full synchronization again. For directory objects (**application**, **administrativeUnit**, **directoryObject**, **directoryRole**, **group**, **orgContact**, **oauth2permissiongrant**, **servicePrincipal**, and **user**), the limit is 7 days. For education objects (**educationSchool**, **educationUser**, and **educationClass**), the limit is 7 days. For Outlook entities (**message**, **mailFolder**, **event**, **contact**, **contactFolder**, **todoTask**, and **todoTaskList**), the upper limit is not fixed; it's dependent on the size of the internal delta token cache. While new delta tokens are continuously added in the cache, after the cache capacity is exceeded, the older delta tokens are deleted.

## Prerequisites

The same [permissions](./permissions-reference.md) that are required to read a specific resource are also required to perform delta query on that resource.

## Delta query request examples

- [Get incremental changes to events in a calendar view](delta-query-events.md)
- [Get incremental changes to messages in a folder](./delta-query-messages.md)
- [Get incremental changes to groups](./delta-query-groups.md)
- [Get incremental changes to users](./delta-query-users.md)
