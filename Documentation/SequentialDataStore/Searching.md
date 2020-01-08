---
uid: sdsSearching
---

# Search in SDS

You can search for texts, fields and others in Sequential Data Store (SDS). The ``GetStreamsAsync``, ``GetTypesAsync``, and ``GetStreamViewsAsync`` overloads return items that match specific search criteria within a given namespace. By default, the query parameter applies to all searchable object fields.

For example, let's say a namespace contains the following streams:

**streamId** | **Name**  | **Description**
------------ | --------- | ----------------
stream1      | tempA     | The temperature from DeviceA
stream2      | pressureA | The pressure from DeviceA
stream3      | calcA     | Calculation from DeviceA values

A ``GetStreamsAsync`` call with different ``Query`` values will return the content below:

**QueryString**     | **Streams returned**
------------------  | ----------------------------------------
``temperature``  | Only stream1 returned.
``calc*``        | Only stream3 returned.
``DeviceA*``     | All three streams returned.
``humidity*``    | No streams returned.

#### Requests
The ``orderby`` parameter supports search in streams and types. Use it to return the result in a sorted order. The default value for this  parameter is ascending. For descending order, specify ``desc`` after the ``orderby`` field value. It can be used in conjunction with ``query``, ``skip``, and ``count`` parameters.

 ```text
	GET api/v1/Tenants/{tenantId}/Namespaces/{namespaceId}/Streams?query=name:pump name:pressure&orderby=name

	GET api/v1/Tenants/{tenantId}/Namespaces/{namespaceId}/Streams?query=name:pump name:pressure&orderby=id asc

	GET api/v1/Tenants/{tenantId}/Namespaces/{namespaceId}/Streams?query=name:pump name:pressure&orderby=name desc

	GET api/v1/Tenants/{tenantId}/Namespaces/{namespaceId}/Streams?query=name:pump name:pressure&orderby=name desc&skip=10&count=20
 ```
##### Parameters
`string query`  
An optional parameter representing a string search. 

`int skip`  
An optional parameter representing the zero-based offset of the first SdsStream to retrieve. 
If not specified, a default value of 0 is used.

`int count`  
An optional parameter representing the maximum number of SdsStreams to retrieve. 
If not specified, a default value of 100 is used.

`string orderby`  
An optional parameter representing sorted order which SdsStreams will be returned. A field name is required. The sorting is based on the stored values for the given field (of type string). For example, ``orderby=name`` would sort the returned results by the ``name`` values (ascending by default). Additionally, a value can be provided along with the field name to identify whether to sort ascending or descending, by using values ``asc`` or ``desc``, respectively. For example, ``orderby=name desc`` would sort the returned results by the ``name`` values, descending. If no value is specified, there is no sorting of results.

#### .NET client libraries method
Use parameters ``skip`` and ``count`` to return what you need when a large number of results match the ``query`` criteria.``count`` indicates the maximum number of items returned by the ``GetStreamsAsync()`` or ``GetTypesAsync()`` call. The maximum value of 
the ``count`` parameter is 1000. ``skip`` indicates the number of matched items to skip over before returning matching items. You use the skip parameter when more items match the search criteria than can be returned in a single call. 

For example, let's say there are 175 streams that match the search criteria: “temperature”.

The call below will return the first 100 matches:
```csharp
    _metadataService.GetStreamsAsync("temperature", 0, 100)
```

By setting ``skip`` to 100, the following call will return the remaining 75 matches, skipping over the first 
100:
```csharp
    _metadataService.GetStreamsAsync("temperature", 100, 100)
```

## Search for streams
Streams search is exposed through the REST API and the client libraries method ``GetStreamsAsync``.

**Searcheable Properties**
| Property          | Searchable  |
|-------------------|-------------|
| Id                | Yes		  |
| TypeId            | Yes		  |
| Name              | Yes		  |
| Description       | Yes		  |
| Indexes           | No		  |
| InterpolationMode | No		  |
| ExtrapolationMode | No		  |
| PropertyOverrides | No		  |

**Searcheable Child Resources**
| Property          | Searchable  |
|-------------------|-------------|
| [Tags](xref:sdsStreamExtra)*		| Yes		  |
| [Metadata](xref:sdsStreamExtra)*	| Yes		  |
| ACL | No		  |
| Owner | No		  |

#### Request
Search for streams using the REST API and specifying the optional `query` parameter:
 ```text
      GET api/v1/Tenants/{tenantId}/Namespaces/{namespaceId}/Streams?query={query}&skip={skip}&count={count}
 ```
##### Parameters
`string query`  
An optional parameter representing a string search. 

`int skip`  
An optional parameter representing the zero-based offset of the first SdsStream to retrieve. 
If not specified, a default value of 0 is used.

`int count`  
An optional parameter representing the maximum number of SdsStreams to retrieve. 
If not specified, a default value of 100 is used.

#### .NET client libraries method
``GetStreamsAsync`` is an overloaded method that is used to search for and return streams. When you call an overloaded method, the software determines the most appropriate method to use by comparing the argument types specified in the call to the method definition.
```csharp
      _metadataService.GetStreamsAsync(query:"QueryString", skip:0, count:100);
```

The Stream fields valid for search are identified in the fields table located on the [Streams](xref:sdsStreams) page. Note that Stream Metadata has unique syntax rules, see [How Searching Works: Stream Metadata](#Stream_Metadata_search_topic).

## Search for types
Types search is exposed through the REST API and the client libraries method ``GetTypesAsync``. 

See [Types](xref:sdsTypes#typepropertiestable) for more information on SdsType properties.
**Searcheable Properties**
| Property          | Searchable |
|-------------------|------------|
| Id                | Yes        |
| Name              | Yes        |
| Description       | Yes        |
| SdsTypeCode       | No         |
| InterpolationMode | No         |
| ExtrapolationMode | No         |
| Properties        | Yes, with limitations* |

The Properties field is identified as being searchable but with limitations: each SdsTypeProperty of a given SdsType has its Name and Id included in the Properties field. This includes nested SdsTypes of the given SdsType. Therefore, the searching of Properties will distinguish SdsTypes by their respective lists of relevant SdsTypeProperty Ids and Names.

#### Request
Search for types using the REST API and specifying the optional `query` parameter:
 ```text
      GET api/v1/Tenants/{tenantId}/Namespaces/{namespaceId}/Types?query={query}&skip={skip}&count={count}
 ```
##### Parameters
`string query`  
An optional parameter representing a string search. 

`int skip`  
An optional parameter representing the zero-based offset of the first SdsType to retrieve. 
If not specified, a default value of 0 is used.

`int count`  
An optional parameter representing the maximum number of SdsTypes to retrieve. 
If not specified, a default value of 100 is used.

#### .NET client libraries method
``GetTypesAsync`` is an overloaded method that is used to search for and return types. 
```csharp
      _metadataService.GetTypesAsync(query:"QueryString", skip:0, count:100);
```
## Search for stream views
Stream views search is exposed through the REST API and the client libraries methodd ``GetStreamViewsAsync``. 

The searchable properties are below. See [Stream Views](xref:sdsStreamViews) for more information.
**Searcheable Properties**
| Property     | Searchable |
|--------------|------------|
| Id           | Yes		|
| Name         | Yes		|
| Description  | Yes		|
| SourceTypeId | Yes		|
| TargetTypeId | Yes		|
| Properties   | Yes, with limitations* |
The Stream View fields valid for search are identified in the fields table located on the [Stream Views](xref:sdsStreamViews) page. The Properties field is identified as being searchable but with limitations because SdsStreamViewProperty objects are not searchable. Only the SdsStreamViewProperty's SdsStreamView is searchable by its Id, SourceTypeId, and TargetTypeId, which are used to return the top level SdsStreamView object when searching. This includes nested SdsStreamViewProperties.

#### Request
Search for stream views using the REST API and specifying the optional `query` parameter:
 ```text
    GET api/v1/Tenants/{tenantId}/Namespaces/{namespaceId}/StreamViews?query={query}&skip={skip}&count={count}
 ```
##### Parameters
`string query`  
An optional parameter representing a stream view search. 

`int skip`  
An optional parameter representing the zero-based offset of the first SdsStreamView to retrieve. 
If not specified, a default value of 0 is used.

`int count`  
An optional parameter representing the maximum number of SdsStreamView to retrieve. 
If not specified, a default value of 100 is used.

#### .NET client libraries method
``GetStreamViewsAsync`` is an overloaded method that is used to search for and return stream views. 
```csharp
    _metadataService.GetStreamViewsAsync(query:"QueryString", skip:0, count:100);
```

## Tokenization
Tokenization is the process of breaking a string sequence into pieces called tokens using specific characters to delimit tokens. User- specified queries are tokenized into search terms. How the query string is tokenized can affect search results.

Delimit the terms with 1) a space or 2) one or more punctuation characters (``*``, ``!``, ``?``, ``.``, for example) and a space. Query string follwed by other punctuation characters without space does not trigger tokenization and is treated as part of the term. 

If your query has a wildcard (``*``) operator after a trailing punctuation, neither the punctuation character or the wildcard operator is tokenized. To specifically search on a term that has trailing punctuation, enclose it in quotation marks 
to ensure the punctuation is part of the query. See examples below:

 Term | Tokenized Term | Details
----------|--------------|-----------
``Device.1`` | ``Device.1``| ``.1`` is included in the token because there is no space between it and ``Device``
``Device!!1`` | ``Device!!1``| ``!!1`` is included in the token because there is no space between it and ``Device``
``Device. ``  | ``Device``| ``.`` and the following space demarcates ``Device`` as the token term
``Device!!`` | ``Device``| 
``Device!*`` | ``Device``| ``!*`` is not included in the token because a wildcard operator following a punctuation character is not tokenized
``"Device!"*`` | ``Device!``


## Search operators
You can use search operators in the ``query`` string to get more refined search results. Operators ``AND``,``OR``, and ``NOT`` must be in all caps. 

Operators | Description
----------|-------------------------------------------------------------------
``AND`` | AND operator. ``cat AND dog`` searches for instances containing both "cat" and "dog".  
``OR``  | OR operator. ``cat OR dog`` searches for instances containing either "cat" or "dog", or both. 
``NOT`` | NOT operator. ``cat NOT dog`` searches for instances with "cat" or without "dog".
``*``   | Wildcard operator. Matches 0 or more characters in a word. ``log*`` searches for terms starting with "log" ("log", "logs" or "logger" for example.); ignores case.
``:``   | Field-scoped query. Specifies a field to search on.  For example, ``id:stream*`` will search for streams where the ``id`` field starts with "stream", but will not search on other fields like ``name`` or ``description``. See 
``" "`` | Quote operator. This searches on an exact sequence of characters rather than searching on words separated by spaces or punctuation.  For example, while ``dog food`` (without quotes) searches for streams containing "dog" or "food" anywhere in any order, ``"dog food"`` (with quotes) will only match streams that contain the whole string together and in that order.
``( )`` | Precedence operator. For example, ``motel AND (wifi OR luxury)`` searches for streams containing "motel" and either "wifi" and/or "luxury".

### : Operator

You can also qualify which fields are searched by using the following syntax: 

	fieldname:fieldvalue

**Request**
 ```text
	GET api/v1/Tenants/{tenantId}/Namespaces/{namespaceId}/Streams?query=name:pump name:pressure
 ```

**.NET Library**
```csharp
	GetStreamsAsync(query:"name:pump name:pressure");
```

### \* Operator

You can use the ``'*'`` character as a wildcard to specify an incomplete string. A wildcard can only be used once for each search term, except for the case of a Contains type query clause. In that case two wildcards are allowed - one at the beginning and one at the end of the term.  For example, ``*Tank*`` is valid but ``*Ta*nk``, ``Ta*nk*``, and ``*Ta*nk*`` are currently not supported.

**Query string**     | **Matches field value** | **Does not match field value**
------------------ | --------------------------------- | -----------------------------
``log*`` | log<br>logger | analog
``*log`` | analog<br>alog | logg
``*log*`` | analog<br>alogger | lop
``l*g`` | log<br>logg | lake swimming

**Supported**     | **Not Supported**
------------------ | ----------------------------------------
``*``<br>``*log``<br>``l*g``<br>``log*``<br>``*log*`` <br>``"my log"*``	| ``*l*g*``<br>``*l*g``<br>``l*g*`` <br>``"my"*"log"``

**Request**
 ```text
	GET api/v1/Tenants/{tenantId}/Namespaces/{namespaceId}/Streams?query=log*
 ```

**.NET Library**
```csharp
	GetStreamsAsync(query:"log*");
```

### \"" Operator	
-----
The search engine automatically searches on strings delimited by whitespace and punctuation.  To search for values that include these delimiters, enclose the value in double quotes.	

When using double quotes, the matching string must include the whole value of the field on the object being searched.  Partial strings will not be matched unless wildcards are used.  For example, if you're searching on a stream that has a description of ``Pump three on unit five``, a query of ``"unit five"`` will not match the description, but a query of ``*"unit five"`` will.

Also, wildcards can be used on the *outside* of the quote operators, but if an asterisk is on the inside of the quotes, it is treated as a string literal rather than a wildcard operator.  For example, you can search for ``"dog food"*`` to find a string that starts with "dog food", but if you search for ``"dog food*"``, it will only match on a value of "dog food*".

 **Query string**     | **Matches field value** | **Does not match field value**	
------------------ | --------------------------------- | -----------------------------	
``"pump pressure"`` | pump pressure | pressure <br> pressure pump <br> pump pressure gauge	
``"pump pressure"*`` | pump pressure <br> pump pressure gauge | pressure <br> pressure pump <br> the pump pressure gauge
``*"pump pressure"`` | pump pressure <br> the pump pressure | pressure <br> pressure pump <br> the pump pressure gauge
``*"pump pressure"*`` | pump pressure <br> pump pressure gauge <br> the pump pressure gauge | pressure <br> pressure pump 
``"pump*pressure"`` | pump\*pressure | pump pressure <br> the pump pressure gauge

 **Request**	
 ```text	
	GET api/v1/Tenants/{tenantId}/Namespaces/{namespaceId}/Streams?query="pump pressure"	
 ```	

 **.NET Library**	
```csharp	
	GetStreamsAsync(query:"pump pressure");	
```	


### Other operators examples
---------------------

**Query string**     | **Matches field value** | **Does not match field value**
------------------ | --------------------------------- | -----------------------------
``mud AND log`` | log mud<br>mud log | mud<br>log
``mud OR log`` | log mud<br>mud<br>log | 
``mud AND (NOT log)`` | mud | mud log
``mud AND (log OR pump*)`` | mud log<br>mud pumps | mud bath
``name:stream* AND (description:pressure OR description:pump)`` | The name starts with "stream" and the description has the either of the terms "pressure" or "pump" | 


## <a name="Stream_Metadata_search_topic">How Searching Works: Stream Metadata</a>

[Stream Metadata](xref:sdsStreamExtra) modifies the aforementioned search syntax rules and each operator's behavior is described below. 
For example, assume that a namespace contains the following Streams and the respective Metadata Key-Value pair(s) for each stream.

**streamId** | **Metadata**
------------ | --------- 
stream1      | { manufacturer, company }<br>{ serial, abc }
stream2      | { serial, a1 }
stream3      | { status, active }<br>{ second key, second value }   
 

### : Operator
-------------------
A Stream Metadata key is only searchable in association with a Stream Metadata value. This pairing is defined using the same  field scoping ``':'`` operator. 

	myStreamMetadataKey:streamMetadataValue

If the ``':'`` operator is not used within an individual search clause then Metadata Keys are not searched against but Metadata 
Values are searched against (along with the other searchable Stream fields).

**QueryString**     | **Streams returned**
------------------  | ----------------------------------------
``manufacturer:company``  | Only stream1 returned.
``company``  | Only stream1 returned.
``a*``  | All three streams returned.

**Request**
 ```text
	GET api/v1/Tenants/{tenantId}/Namespaces/{namespaceId}/Streams?query=manufacturer:company
 ```

**.NET Library**
```csharp
	GetStreamsAsync(query:"manufacturer:company");
```

### \* Operator  

For searching on Metadata values the ``'*'`` character is again used as a wildcard to specify an incomplete string. Additionally,
this wildcard character can be used with the Metadata key as well. This is not supported for any other "fields", so by including a wildcard in a field
(defined as a value to the immediate left of a ``':'`` operator) the query will only be valid against Stream Metadata.

**QueryString**     | **Streams returned**
------------------  | ----------------------------------------
``manufa*turer:compan*``  | Only stream1 returned.
``ser*al:a*``  | Stream1 and stream2 are returned.
``s*:a*``  | All three streams returned.
``Id:stream*``  |  All three streams returned.
``Id*:stream*``  | Nothing returned.

Note that in the final example nothing matches on a Stream's Id value because including ``'*'`` in a search clause's 
field prevents non-Stream Metadata fields from being searched.

**Request**
 ```text
	GET api/v1/Tenants/{tenantId}/Namespaces/{namespaceId}/Streams?query=manufa*turer:compan*
 ```

**.NET Library**
```csharp
	GetStreamsAsync(query:"manufa*turer:compan*");
```

