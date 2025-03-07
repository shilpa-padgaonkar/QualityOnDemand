# Overview

<span class="colour" style="color:rgb(0, 0, 0)">The Quality-On-Demand (QoD) API for stable latency provides programmable interface for developers and other users (capabilities consumers) to specify stable data-transfer latency managed by Telco networks without necessity to have in-depth knowledge of the 4G/5G system complexity by abstracting the internal complexity of telecom systems [1].</span>

## 1\. Introduction

Industrial (IoT), VR/Gaming, broadcasting, autonomous driving and many others scenarios demand network communication quality and are sensitive to any change in transmission conditions. Being able to request a stable latency (reduced jitter) from the network can improve user experience.

The QoD latency API offers the application developers the capability to request for stable latency (reduced jitter) for a specified App-Flow between User Equipment (application clients) and Application Servers (backend services). The developer has a pre-defined set of QoS\_Profiles which he could choose from depending on his latency requirements.

<img src="./resources/QoD_latency_overview.PNG" alt="QoD_LM" title="QoD Latency API Overview">

## 2\. Quick Start

The usage of the stable latency API is based on QoS sessions, which can be created (based on available QoS profiles), queried and deleted.
The deletion of a requested session can be triggered by the user or can be triggered automatically. The automatic process is triggered either when the user specified duration has reached its limit or default session expiration time has been reached (witihin reference implementation set to 24hrs).

Before starting to use the API, the developer needs to know about the below specified details:

**Base-URL**
The RESTful Stable Throughput API endpoint, for example [**https://telekom-api.developer.telekom.com/5g-latency**](https://telekom-api.developer.telekom.com/5g-throughput)

**Authentication**
Configure security access keys such as OAuth 2.0 client credentials to be used by Client applications which will invoke the QoD API.

**QoS Profile**
Define latency requirements of the application and identify QoS profile class which maps into the required performance category.

**App-Flow**
Describes the precise flow the developer wants to prioritize and have stable latency for. This flow is described using source and destination IP addresses, ports and protocols with flow direction.

**Duration**
Define the number of seconds for which the QoD session should be created. This parameter is optional and if not specified, the session is either deleted on user request or if default expiration limit has been reached (24 hours in reference implmentation).

**Notification URL and token**
Developers have a chance to specify callback URL on which notifications (eg. session termination) regarding the session can be received from the service provider. This is also an optional parameter.

Sample API invocations are presented in Section 4.5.

## 3\. Authentication and Authorization

The QoD Service API makes use of the client credentials grant which is applicable for server to server use cases involving trusted partners
or clients without any protected user data involved.
In this method the API invoker client is registered as a confidential client with an authorization grant type of client\_credentials [3].

## 4\. API Documentation

### 4.1 Details

The usage of the QoD latency API is based on QoS profile classes and parameters which define App-Flows.
Based on the API, QoS sessions can be created, queried, and deleted. Once an offered QoS profile latency class is requested, application users get a prioritized service with stable latency even in the case of congestion.The QoD latency API has the following characteristics:

* A specified App-Flow is prioritized to ensure stable latency for that flow
* The prioritized App-Flow is described by providing additional information such as protocols, ports, uplink/downlink direction of flow etc.
* Stable latency is requested by selecting from the list of QoS profiles made available by the service provider (e.g. LOW) to map latency requirements
* The developer can optionally specify the duration for which he needs the prioritized App-flow
* The developer can optionally also specify callback URL on which notifications for the session can be sent
Following diagram shows the interaction between different components

<br>

<img src="./resources/QoD_latency_details.PNG" alt="QoD_LM" title="QoD Latency Management API">

The below table shows sample QoS profiles and are subject to service provider customizations.
This sample is taken from the agreed sample (example) set from the Camara-project [2].

| **QoD latency profile** | **Details** |
| ------------------- | ------- |
| LOW\_Latency | App-Flow with stable "latency" under congestion (e.g. throughput up-to 2Mbps) |

### 4.2 Endpoint Definitions

Following table defines API endpoints of exposed REST based for QoD latency management operations.

| **Endpoint** | **Operation** | **Description** |
| -------- | --------- | ----------- |
| POST<br>  \<base-url>/qod-latency-api/v0/sessions | **Create Latency Session** | Create QoS Session to manage latency priorities |
| GET<br> \<base-url>/qod-latency-api/v0/sessions/{sessionId} | **Query for Latency** | Querying for QoS "latency" session information details |
| DELETE<br> \<base-url>/qod-latency-api/v0/sessions/{sessionId} | **Delete Latency Session** | Deleting a QoS "latency" session |
<br>

#### QoD Create Latency QoS Session Operation

| **Create Latency QoS Session** |
| -------------------------- |
| **HTTP Request**<br> POST \<base-url>/qod-latency-api/v0/sessions<br>**Query Parameters**<br> No query parameters are defined.<br>**Path Parameters**<br> No path parameters are defined.<br>**Request Body Parameters**<br> **duration (optional)**: Session duration in seconds. Maximal value of 24 hours is used if not set.<br> **ueAddr:** The IPv4 address of the user equipment. It can contain a single IP address or a range, using a mask.<br>  Format: \<address>[/\<mask>]<br>   - address : an IPv4 number in dotted-quad form 1.2.3.4. Only this exact IP number will match the flow control rule.<br>   - address/mask : an IP number as above with a mask width of the form 1.2.3.4/24.<br>    *In this case, all IP numbers from 1.2.3.0 to 1.2.3.255 will match. The bit width MUST be valid for the IP  version.*<br> **asAddr:** The IPv4 address of the application server. It can contain a single IP address or a range, using a mask.<br> <br> **uePort (optional):** A list of single ports or port ranges on the user equipment.<br>  Ports may be specified as <\{port\|port\-port\}\[\,ports\[\,\.\.\.\]\]\>\.<br>   The '-' notation specifies a range of ports (including boundaries).<br>   Example: '5010-5020,5021,5022'<br> **asPort (optional):** A list of single ports or port ranges on the application server.<br> **protocolIn:** The used transport protocol for the uplink.<br>  TCP - TCP protocol<br>  UDP - UDP protocol<br>  ANY - all protocols<br> **protocolOut :** The used transport protocol for the downlink.<br>  TCP - TCP protocol<br>  UDP - UDP protocol<br>  ANY - all protocols<br> **qos:** Qualifier for the requested latency profile.<br>LOW\_LATENCY - to request the stable latency<br> **notificationUri (optional):** URI of the callback receiver. Allows asynchronous delivery of session related events .<br><span class="s1">&nbsp; Example: '[https://application-server.com/notifications](https://application-server.com/notifications)'</span><br> **notificationAuthToken (optional):** Authentification token for callback API.<br>  Example: 'c8974e592c2fa383d4a3960714'<br><br>**Response**<br> **201: Session created**<br>  Response body:<br>   **duration:** Session duration in seconds.<br>   **ueAddr:** The ipv4 address of the user equipment.<br>   **asAddr:** The ipv4 address of the application server.<br>   **uePort (optional):** The requested port(s) on the user equipment.<br>   **asPort (optional):** The requested port(s) on the user equipment.<br>   **protocolIn:** The used transport protocol for the uplink.<br>   **protocolOut:** The used transport protocol for the downlink.<br>   **qos:** Qualifier of the requested throughput profile.<br>   **notificationUri (optional):** URI of the callback receiver.<br>   **notificationAuthToken (optional):** Authentication token for callback API.<br>   **id:** Session ID in UUID format.<br>    Example: 123e4567-e89b-12d3-a456-426614174000<br>   **startedAt:** Timestamp of session start, in seconds since unix epoch.<br>    Example: 1639479600<br>   **expiresAt**: Timestamp of session expiration if the session was not deleted, in seconds since unix epoch.<br><br> **400:** **Invalid input.**<br> **401:** **Un-authorized, missing or incorrect authentication.**<br> **405:** **Invalid input**<br> **500:** **Session not created**<br> **503:** **Service temporarily unavailable** |
<br>

#### QoD Query for Latency QoS Session

| **Quering QoS Session Latency information** |
| --------------------------------------- |
| **HTTP Request**<br> GET\<base-url>/qod-latency-api/v0/sessions/{sessionId}<br>**Query Parameters**<br> No query parameters are defined.<br>**Path Parameters**<br> sessionId: Session id that was obtained from the Create QoS Session operation.<br>**Request Body Parameters**<br> No request body parameters are defined.<br>**Response**<br><br> **200: Session information returned.**<br>  Response body:<br>   **duration:** Session duration in seconds.<br>   **ueAddr:** The ipv4 address of the user equipment.<br>   **asAddr:** The ipv4 address of the application server.<br>   **uePort (optional):** The requested port(s) on the user equipment.<br>   **asPort (optional):** The requested port(s) on the user equipment.<br>   **protocolIn:** The used transport protocol for the uplink.<br>   **protocolOut:** The used transport protocol for the downlink.<br>   **qos:** Qualifier of the requested Latency profile.<br>   **notificationUri (optional):** URI of the callback receiver.<br>   **notificationAuthToken (optional):** Authentication token for callback API.<br>   **id:** Session ID in UUID format.<br>   **startedAt:** Timestamp of session start in seconds since unix epoch.<br>   **expiresAt:** Timestamp of session expiration if the session was not deleted in seconds since unix epoch.<br><br> **401:** Un-authorised, missing or incorrect authentication.<br> **404:** Session not found.<br> **503:** Service temporarily unavailable. |
<br>

#### QoD Delete Latency QoS Session

| **Deleting QoS Latency session** |
| ---------------------------- |
| **HTTP Request**<br>  DELETE\<base-url>/qod-latency-api/v0/sessions/{sessionId}<br>**Query Parameters**<br>  No query parameters are defined.<br>**Path Parameters**<br>  sessionId: Session ID that need to terminated.<br>**Request Body Parameters**<br>  No request body parameters are defined.<br><br>**Response**<br> **204:** Session deleted<br> **401:** Un-authorized, missing or incorrect authentication.<br> **404:** Session not found |

### 4.3 Errors

Since CAMARA QoD API is based on REST design principles and blueprints, well defined HTTP status
codes and families specified by community are followed [4].

Details of HTTP based error/exception codes for the QoD API are described in Section 4.2 of each API REST based method.
Following table provides an overview of common error names, codes and messages applicable to QoD API.

| No | Error Name | Error Code | Error Message |
| --- | ---------- | ---------- | ------------- |
| 1 | Invalid port(s) | 400 | "Ports specification not valid |
| 2 | Invalid protocol | 400 | "Validation failed for parameter: protocol" |
| 3 | Invalid QoS profile | 400 | "Validation failed for parameter: QoS-profile" |
| 4 | Invalid IP address (format) | 400 | "Validation failed for parameter: IP-addr" |
| 5 | Invalid duration | 400 | "Validation failed for parameter: Session duration" |
| 6 | Unauthorized | 401 | "Un-authorized to invoke operation" |
| 7 | Forbidden | 403 | "Forbidden to invoke operation" |
| 8 | Session with same parameters already exists | 409 | "Found session \<session> already active until \<expirationTime>" |
| 9 | Service unavailable | 503 | “Internal error due to requrired telco service unvailability" |

### 4.4 Policies

N/A

### 4.5 Code Snippets
<br>
<span class="colour" style="color:rgb(36, 41, 47)">Snippet 1, elaborates REST based API call with "*curl"* to create a QoS session for sample streaming service with following parameters: </span>

* Latency QoS session with 1H duration and QoS-profile "LOW\_Latency" mapping,
* App-Flow is specified for UDP protocol with UE-Terminal IP address (ueAddr=10.0.0.1), Application server network (asAddr=54.204.25.0/28) and Port number (asPorts=33001).

Please note, the credentials for API authentication purposes need to be adjusted based on target security system configuration.

| Snippet 1. Create QoS session to manage latency |
| ----------------------------------------------- |
| curl -X 'POST' `https://sample-base-url/qod-latency-api/v0/sessions`   <br>    -H 'accept: application/json' <br>    -H 'Content-Type: application/json'<br>    -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbG...."<br>    -d '{<br>     "duration": 3600,<br>     "ueAddr": "10.0.0.1",<br>     "asAddr": "54.204.25.0/28",<br>     "asPorts": "33001",<br>     "protocolOut": "UDP",<br>     "qos": "LOW\_Latency",<br>     "notificationUri": `https://your-callback-server.com/notifications`,<br>     "notificationAuthToken": "c8974e592c2fa383d4a3960714"<br>   }' |
<br>
Snippet 2, elaborates sample QoS notification "SESSION\_TERMINATION" message distributed from QoD backend to client callback function.

| Snippet 2. Sample QoS session notification |
| ------------------------------------------ |
| {<br>   "sessionId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",   "event": "SESSION\_TERMINATED"<br>} |

### 4.6 FAQ's

(FAQs will be added in a later version of the documentation)

### 4.7 Terms

N/A

### 4.8 Release Notes

N/A

## References

[1] 3GPP TS 23.501: System architecture for the 5G System (5GS); Stage 2 (Release 17), V17.4.0 (2022-03)<br>
[2] Camara QoS/QCI mapping table https://github.com/camaraproject/QualityOnDemand/blob/main/code/API_definitions/QoSProfile_Mapping_Table.md <br>
[3] Camara Commonalities : Authentication and Authorization Concept for Service APIs https://github.com/camaraproject/WorkingGroups/blob/main/Commonalities/documentation/Working/CAMARA-AuthN-AuthZ-Concept.md <br>
[4] HTTP Status codes spec https://restfulapi.net/http-status-codes/
