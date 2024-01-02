# Specification of protocol JSON-RPC M1

| Setting            | Value                                              |
|--------------------|----------------------------------------------------|
| **Field**          | Information Technology                             |
| **Subject**        | Protocol                                           |
| **Name**           | JSON-RPC M1                                        |
| **Document Type**  | Specification                                      |
| **Revision**       | 1 (One)                                            |
| **Origin Date**    | 2024-01-02 _(Second of January, year 2024)_        |
| **Author**         | Vault Thirteen (https://github.com/vault-thirteen) |
| **Changes**        | None                                               |
| **Last Edit Date** | None                                               |


### Table of contents
<!-- TOC -->
* [Specification of protocol JSON-RPC M1](#specification-of-protocol-json-rpc-m1)
    * [Table of contents](#table-of-contents)
* [1. Overview](#1-overview)
* [2. Conventions](#2-conventions)
* [3. Request](#3-request)
  * [Notes on Request](#notes-on-request)
    * [3.1. Mandatory Fields](#31-mandatory-fields)
    * [3.2. Protocol Version](#32-protocol-version)
    * [3.3. Request Identifier](#33-request-identifier)
    * [3.4. Method Name](#34-method-name)
    * [3.5. Function Parameters](#35-function-parameters)
* [4. Response](#4-response)
  * [Notes on Response](#notes-on-response)
    * [4.1. Response Fields](#41-response-fields)
    * [4.2. Protocol Version](#42-protocol-version)
    * [4.3. Request & Response Identifier](#43-request--response-identifier)
    * [4.4. Result](#44-result)
    * [4.5. Error](#45-error)
    * [4.6. Success Marker](#46-success-marker)
    * [4.7. Meta Information](#47-meta-information)
* [5. General Notes](#5-general-notes)
    * [5.1. Object Field Order](#51-object-field-order)
    * [5.2. Object Field Names](#52-object-field-names)
    * [5.3. Error Codes](#53-error-codes)
    * [5.4. Software Implementation](#54-software-implementation)
    * [5.5. Effectiveness](#55-effectiveness)
<!-- TOC -->

# 1. Overview

_JSON-RPC M1_ is a remote procedure call protocol.

It is a modification of the original _JSON-RPC 2.0_ protocol which can be found on its website – https://www.jsonrpc.org.

_JSON-RPC M1_ remote procedure call protocol is stateless. It is light-weight and very simple. It is even more simple than the original _JSON-RPC 2.0_ protocol. As many people know, the simpler the thing is – the more reliable it is.

_JSON-RPC M1_ remote procedure call protocol is based on _JavaScript Object Notation_ also known as _JSON_ which is described in RFC 4627 and on its website located at http://www.json.org.

For additional robustness, _JSON-RPC M1_ protocol uses more strict typing than in the original _JSON_ Specification.

# 2. Conventions

_JSON-RPC M1_ protocol specification, further referred to as the **_M1 Specification_**, describes the process of communication between a server and a client. One or more clients make requests to a server and a server must serve all the requests of each client. To serve a client's request, the server reads client's request, performs an action stated in the request and responds to the client. This means that process of communication consists of requests being sent from a client to a server and responses being sent from a server to a client. Requests and responses are described below.

Requests and responses must be encoded with the same text encoding. Text encoding of requests and responses is not limited by this specification, but it is advised to be the _UTF-8_ encoding of the _Unicode_ standard while it is one of the most popular encodings in the World Wide Web.

# 3. Request

Each request is a textual message encoded in a standard _JSON_ format.  
Request is a _JSON_ object which must have exactly four (4) mandatory fields:

* `jsonrpc`
* `id`
* `method`
* `params`

These four fields of a client request and their meanings are described below.

| Field       | Type   | Meaning                                                                 | Example                      |
|-------------|--------|-------------------------------------------------------------------------|------------------------------|
| **jsonrpc** | String | A _JSON_ string containing protocol version. A typical version is `M1`. | `"jsonrpc": "M1"`            |
| **id**      | String | A _JSON_ string identifying the request.                                | `"id": "12345"`              |
| **method**  | String | A _JSON_ string containing a name of the requested function.            | `"method": "setXY"`          |
| **params**  | Object | A _JSON_ object containing named arguments for the requested function.  | `"params": {"x": 6, "y": 9}` |

## Notes on Request

### 3.1. Mandatory Fields

Being a mandatory field means that _JSON_ `Null` value is not accepted as a field value.

### 3.2. Protocol Version

A typical protocol version is `M1`.

If a client request contains an invalid or unsupported protocol version, an error must be returned.

### 3.3. Request Identifier

As opposed to the original _JSON-RPC 2.0_ protocol, requests made with _M1 Specification_ require the `id` field to be set. This also means that _M1 Specification_ does not support one-side messages of the _JSON-RPC 2.0_ protocol. All client requests of the _M1 Specification_ must have a corresponding server response, _JSON-RPC 2.0_ notifications are not supported.

It is a good practice to use unique values for request identifiers where possible.

If a client request contains an invalid request identifier, an error must be returned.

### 3.4. Method Name

Method name must contain only allowed characters. The list of allowed characters consists of the following symbols:
* small and capital latin letters of the _ASCII_ standard
  * small letters from `a` to `z`
  * capital letters from `A` to `Z`
* _ASCII_ numbers
  * numbers from `0` to `9`
* _ASCII_ `Low Line` symbol, also known as underscore.

If a client request contains either invalid or non-existent function name, an error must be returned.

### 3.5. Function Parameters

If the called function or procedure does not accept any parameters, an empty _JSON_ object must be provided as `params` value (`"params": {}`).

If a client request contains function parameters different from parameters of the called function, an error must be returned. For example, if a function has a single parameter named `x` and client provides two parameters named `x` and `y`, the server must respond with an error stating the unsupported parameter `y`.

If a client does not provide a required function parameter `x`, the server, if possible, should respond with an error stating the missing `x` parameter which was not provided. Unfortunately, a lot of libraries for parsing _JSON_ format do not provide this functionality, that is why this rule says _SHOULD_ instead of _MUST_.

Passing function parameters as an array instead of an object is not allowed by the _M1 Specification_. If an array must be passed to a function, it should be placed into a named argument, i.e. into a named field of an object.

# 4. Response

Each client request must be followed by a server response.  
Each response is a textual message encoded in a standard _JSON_ format.  
Response is a _JSON_ object which must have at least five (5) following fields:

* `jsonrpc`
* `id`
* `result`
* `error`
* `ok`

These five required fields of a server response and their meanings are described below.

| Field       | Type    | Meaning                                                                         | Example                    |
|-------------|---------|---------------------------------------------------------------------------------|----------------------------|
| **jsonrpc** | String  | A _JSON_ string containing protocol version. A typical version is `M1`.         | `"jsonrpc": "M1"`          |
| **id**      | String  | A _JSON_ string identifying the request and response to it.                     | `"id": "12345"`            |
| **result**  | Object  | A _JSON_ object containing the result returned by the requested function.       | `"result": {"answer": 42}` |
| **error**   | Object  | A _JSON_ object containing error returned by the requested function.            | `"error": {...}`           |
| **ok**      | Boolean | A _JSON_ boolean indicating the successful execution of the requested function. | `"ok": true`               |

## Notes on Response

### 4.1. Response Fields

All the above-mentioned five response fields must be set explicitly, even if they contain the _JSON_ `Null` value.

### 4.2. Protocol Version

Protocol version in a response must be the same as in a normal request. Server must respond only with a valid protocol version even if the protocol version in the client request is wrong. If a client request contains an invalid or unsupported protocol version, an error must be returned.

### 4.3. Request & Response Identifier

Request identifier in response must be the same as in a normal request to which a server is responding. If the server can not obtain the request identifier, it must respond with an error and a request identifier set to _JSON_ `Null`.

### 4.4. Result

The result returned by the called function or procedure must be a _JSON_ object with named fields being the result values returned by the called function or procedure. An example – `"result": {"x": 1, "y": 2}`. If the called function or procedure does not return anything, the server must respond with an empty _JSON_ object (`"result": {}`). If execution of the requested function or procedure fails or ends with an error, server must set the result to _JSON_ `Null` (`"result": null`).

### 4.5. Error

The error returned by the called function or procedure must be a _JSON_ object with three following fields.

| Field       | Type    | Meaning                                                  | Example             |
|-------------|---------|----------------------------------------------------------|---------------------|
| **code**    | Integer | A _JSON_ integer number with a numeric error code.       | `"code": 101`       |
| **message** | String  | A _JSON_ string with a textual description of the error. | `"message": "oops"` |
| **data**    | Value   | A _JSON_ value providing error details.                  | `"data": ...`       |

**Notes on Error**

**4.5.1. Error Code**

Error code must be an integer number. Positive error code numbers may be used for errors defined by user functions. Negative error code numbers are used by errors defined by an RPC server. Zero number is never used as an error code. Error code is a required field when an error is set.

**4.5.2. Error Message**

Error message may be anything in textual format. Error message is a required field when an error is set.

**4.5.3. Error Data**

Error data is an optional _JSON_ value providing additional details about the error.
If no details are available, it must be _JSON_ `Null` (`"data": null`).

**4.5.4. Error Object**

If execution of the called function or procedure finishes successfully without any errors, server must return the _JSON_ `Null` instead of the error object (`"error": null`).

### 4.6. Success Marker

If execution of the called function or procedure finishes successfully without any error, the server must set the success marker to _JSON_ `True` value. (`"ok": true`) If execution of the called function or procedure fails or does not finish successfully, the server must set the success marker to _JSON_ `False` value (`"ok": false`).

### 4.7. Meta Information

For purposes of transmission of information not directly related to the called function or procedure, the response object may have an additional field named `meta`. This field must be of _JSON_ object type and may be used to store additional information about the call, such as information about the time and duration of the call, environmental setup and other parameters.

The `meta` field can not be an empty object. It means that if no meta information is provided, `meta` field must be _JSON_ `Null` (`"meta": null`). An empty `meta` field (_JSON_ `Null`) should be omitted in response. 

| Field    | Type   | Meaning                                                     | Example         |
|----------|--------|-------------------------------------------------------------|-----------------|
| **meta** | Object | A _JSON_ object containing meta information about the call. | `"meta": {...}` |

# 5. General Notes

### 5.1. Object Field Order

Field order in request and response objects is not limited by the _M1 Specification_, however it is advised that libraries implementing this specification use the strict field order similar to the one stated here. The reason for this is very simple. It is recommended to have the boolean success marker as the last field in a response to indicate the correctness of the message delivery.

### 5.2. Object Field Names

Request and response objects must contain only those root fields which are described in this specification. It means that request object can not have a root field named e.g. `time`, response object can not have a root field named e.g. `weather`. All the additional information should be transmitted with the help of meta-data fields.

### 5.3. Error Codes

Each implementation of an RPC server using the _JSON-RPC M1_ specification must support following error codes.

| Code | Short Name               | Description                                                      | 
|------|--------------------------|------------------------------------------------------------------|
| -1   | Request is not readable. | Request text is not parsable as a valid _JSON_ message.          |
| -2   | Invalid request.         | Received _JSON_ is valid, but request object is not valid.       |
| -4   | Unsupported protocol.    | Requested RPC protocol is not supported by server.               |
| -8   | Unknown method.          | Requested RPC method is not known to server.                     |
| -16  | Invalid parameters.      | Method parameters are invalid or contain an error.               |
| -32  | Internal RPC error.      | An internal RPC server error or an exception during method call. |

Error codes `-64`, `-128` and `-256` are reserved for possible future revisions of this document and may not be used by an RPC server.

Error codes of `1` and all greater positive integer numbers may be used by errors generated by RPC user functions.

### 5.4. Software Implementation

To implement the described logics of request and response objects it is advised to use pointers to object while they can implement all three states of object fields. Following is an example how to implement the `result` field of a response object.

* **Case I.** When the called function returns some values, the `result` is a pointer to an object which is fully set.
```json
{
	"result":
	{
		"name": "John",
		"age": 100
	}
}
```

* **Case II.** When the called function returns nothing, the `result` is a pointer to an empty object, i.e. an object with no fields.
```json
{
	"result": {}
}
```

* **Case III.** When function call fails, the result is a null pointer.
```json
{
	"result": null
}
```

### 5.5. Effectiveness

This specification of protocol _JSON-RPC M1_ is compiled in a single copy uploaded to the _GitHub_ repository named _Vault Thirteen_ which is located at the Internet address `github.com/vault-thirteen`. No other copies of this document are effective and not one of the copies can be made effective while this original copy exists at the specified place.

---

Copyright © 2024 by the authors of the _Vault Thirteen_
(https://github.com/vault-thirteen) _GitHub_ repository.

This document and the information contained herein is provided "AS IS" and ALL WARRANTIES, EXPRESS OR IMPLIED are DISCLAIMED, INCLUDING BUT NOT LIMITED TO ANY WARRANTY THAT THE USE OF THE INFORMATION HEREIN WILL NOT INFRINGE ANY RIGHTS OR ANY IMPLIED WARRANTIES OF MERCHANTABILITY OR FITNESS FOR A PARTICULAR PURPOSE.
