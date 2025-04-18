DIGIT Registries
Work in Progress
# Objective
The objective of this document is to define the core principles, architecture, and implementation approach for registries within the DIGIT platform, ensuring they serve as authoritative, interoperable, and scalable data repositories. By outlining the essential characteristics, design principles, and challenges of government registries, the document aims to guide the development of a federated and decentralized registry framework that enhances data integrity, service efficiency, and secure information exchange across departments. Additionally, it evaluates different architectural approaches—separate microservices, a common registry service, and a hybrid model—to recommend a scalable and maintainable solution that aligns with government digital transformation objectives.
# Introduction:
Governments manage vast amounts of critical data across various domains, including citizen identities, properties, businesses, and public services. Registries serve as authoritative repositories that ensure accuracy, reliability, and security, acting as a single source of truth for governance. However, many government agencies still operate in data silos, leading to duplication, inconsistencies, and inefficiencies in service delivery. The increasing need for interdepartmental data exchange has made it essential for governments to adopt modern registry frameworks that enable secure, interoperable, and policy-driven information sharing while maintaining department-level autonomy.
A modern registry system must be federated and decentralized, allowing each department to manage its own data while ensuring secure authentication, consent-based data access, and standardized interoperability. By leveraging unique identifiers, scalable architectures, and real-time integration mechanisms, registries can improve data integrity, streamline service delivery, and enhance citizen experiences. As governments embrace digital transformation, efficient, trustworthy, and interconnected registries will become the backbone of transparent and responsive governance.

## Core Characteristics of a Registry Service

A registry should:

### 1. Be Authoritative: Serve as the Source of Truth

- The registry should be the most reliable place for information.
- Other systems or people should trust the registry's data as accurate and up-to-date.

> Imagine you’re checking land ownership. If the property registry says a person owns a piece of land, that should be the final word. There shouldn’t be any confusion or conflicting information elsewhere.

**Example:**  
In a Property Registry, if it shows that "Plot No. 101" belongs to "John Doe," no other system or office should have different information about that property.

- It gets regular updates from verified sources (e.g., property purchases, legal changes).
- Data is validated before being saved, ensuring accuracy.

### 2. Support CRUD Operations: Create, Read, Update, and Delete

The registry allows users to:

- **Create** new records (e.g., registering a new property).
- **Read** or view existing records.
- **Update** existing information (e.g., changing the owner’s name).
- **Delete** records that are no longer needed (with checks to avoid accidental deletion).

### 3. Provide Search & Query Capabilities

- Users should be able to quickly find information by searching for specific details.
- Filters can narrow down results (e.g., by location, owner name, status).
- Makes it easy to find relevant information without manually browsing through all records.

**Example:**  
In a Water Connection Registry:
- A user can search by property address to find water connection details.
- A municipality officer can filter connections by status (e.g., active, inactive) to manage maintenance schedules.

- The registry uses a search engine or database queries to fetch relevant data.
- Results are displayed in an organized list with sorting and filtering options.

### 4. Uniquely Identifiable

Unique identity ensures that each record in the registry is distinct, just like how fingerprints or national IDs uniquely identify individuals. It involves using two types of identifiers:

- **Business Unique ID**: Specific to the domain and meaningful to users (e.g., Property Number, National ID).  
  This is a human-readable identifier that is relevant to the domain and ensures uniqueness based on business rules.  
  For example, a Property ID like `PROP-2025-101` or a National ID for individuals.  
  *Probabilistic Matching* – A combination of attributes is used to guarantee uniqueness.

- **System-Generated UUID**: Internally generated for system reference, ensuring global uniqueness.  
  This is a globally unique identifier generated by the system.  
  It is used internally for referencing records in the database and APIs.  
  UUIDs ensure uniqueness even if data is distributed across multiple servers or systems.

#### Benefits of Unique ID

- **Prevents Duplicate Entries**: Ensures no duplicate records, which maintains data integrity.
- **Accurate Record-Keeping**: Avoids confusion, ensuring reliable data retrieval and updates.
- **Easy Reference**: Both business users and systems can refer to records without ambiguity.

> ID can also be the address like in a URL — `http://egov.org.in` is unique and can also be resolved to the information, i.e., the webpage content.

- **Consistency Across Systems**: If data is shared or integrated with other systems, unique identifiers maintain consistency.

#### Examples:

**1. Property Registry Example**  
**Scenario**: Registering a property in a city.

**Unique Identifiers Used**:
- **Business Unique ID**: Property Number (e.g., `PROP-2025-101`)  
  This is meaningful to users, helping them easily identify the property.  
  It includes the year of registration (`2025`) and a unique serial number (`101`).

- **System-Generated UUID**:  
  Example: `3f50d9b7-e7ba-4e04-a13f-9d3d24b3a5b9`  
  Used internally for database operations and APIs.

When a new property is registered:
- The system checks the Property Number for uniqueness within that year.
- A UUID is also generated, ensuring global uniqueness across all properties.

> Even if two properties share the same owner and address, their UUIDs and Property Numbers are different.

**2. Individual Registry Example**  
**Scenario**: Registering a person in an individual registry.

**Unique Identifiers Used**:

- **Business Unique ID**: National ID or a combination of attributes.
  - If National ID is present, it is used (e.g., `"A123456789"`).
  - If National ID is not present, a combination of attributes ensures uniqueness:
    - Name + Date of Birth + Place of Birth (e.g., `"JohnDoe-1990-01-01-CityXYZ"`).

- **System-Generated UUID**:  
  Example: `c12e1d48-4f59-4748-b2d9-4d2f4f2c9c9d`  
  Used internally for secure data operations.

**How it works**:
- If a person provides their National ID, the system checks it against existing records to prevent duplicates.
- If no National ID is given, the system verifies uniqueness using the Name + Date of Birth + Place of Birth combination.
- A UUID is generated for system reference.

## 5. Traceability

The registry keeps track of:
- **Who made changes** (e.g., user ID).
- **When the changes were made** (timestamp).
- **What changes were made** (before and after values).

### Why It’s Important:
- **Ensures accountability and transparency**.
- **Helps in auditing and resolving disputes**.

### Example:
In a **Trade License Registry**:
- If a license's validity date is changed, the system logs:
  - **Who changed it**: Admin User (user ID or role).
  - **When it was changed**: Feb 13, 2025, 10:00 AM.
  - **What was changed**: From 2024-12-31 to 2025-12-31.

### How It Works:
1. **Audit Log**: Every change is recorded in a dedicated audit log.
2. **Change Details**: The log captures the "before" and "after" values along with metadata (who, when).
3. **Access Control**: The log is secured against unauthorized access or tampering, ensuring only authorized users can view or manage it.
4. **Data Integrity**: Measures like encryption or digital signatures may be applied to further secure the integrity of the audit trail.

## 6. Interoperability

The registry can communicate and share data with other systems or platforms. It follows standardized data formats (like JSON, XML) for seamless integration.

### Why It’s Important:
- **Ensures consistency and collaboration** between systems (e.g., tax, billing, and legal departments).
- **Reduces duplication of data entry and errors**.

### Example:
In a **Property Registry**:
- When a new property is registered, the registry automatically informs the tax system to update property tax details.
- It can also send property details to the water connection registry for service activation.

### How It Works:
- **APIs (Application Programming Interfaces)** enable secure data exchange between systems.
- **Webhooks or events** notify other systems of changes in real time, ensuring that updates are reflected across all platforms.

## 7. Scalable & Performant

The registry can handle increasing amounts of data and user requests without slowing down. It is built to accommodate growth, both in terms of storage and user interactions.

### Why It’s Important:
- **Ensures consistent performance** even as more users or data are added.
- **Prevents system crashes** during high usage periods (e.g., end-of-year property tax filings).

### Example:
In a **Water Connection Registry**:
- It efficiently handles thousands of new connections during a city expansion.
- Users can search and view connection details without delays, even during peak hours.

### How It Works:
- **Distributed Databases**: Used to store and manage large datasets across multiple nodes, ensuring data scalability.
- **Load Balancers**: Distribute user requests across multiple servers to avoid overload on any single server.
- **Caching Mechanisms**: Speed up frequently accessed data, reducing response times and load on backend systems.

## 8. Authentication (Record Existence Check)

Allows applications to verify whether a record exists in the registry without exposing sensitive details. It returns a Yes/No response based on the validity of the provided identifier (e.g., UUID, DID, cryptographic proof). This response is returned as a cryptographically signed authentication token (e.g., JWT, DID Proof, or VC).

Supports various authentication methods:
- **Passwordless Authentication**
- **Token-based Authentication** (OAuth2, JWT)
- **DID-based Authentication**
- **OTP Verification**

### Why It’s Important:
- **Secure, lightweight verification**: Ensures that no private information is exposed during the verification process.
- **Reduces data redundancy**: External systems can confirm the existence of a record without duplicating data.

### Example:
A **Property Tax System** queries the **Property Registry** to verify if a property ID exists before generating a tax assessment. The system exposes an `/registry/authenticate` API that:
- Takes a record identifier (e.g., property ID).
- Returns either "Yes" (record exists) or "No" (record not found).

## 9. eKYC (Verified Data Retrieval)

Enables applications to request verified data from a registry with explicit consent. Ensures compliance with security policies, including role-based (RBAC) or attribute-based (ABAC) access control. 

### Key Features:
- **Supports Verifiable Credentials (VCs)** and cryptographic signatures to guarantee data authenticity.
- Facilitates **trusted data exchange** across federated registries.
- Protects **sensitive information** by ensuring only authorized entities can access verified details.
- Reduces reliance on manual verification processes, improving efficiency and security.

### Why It’s Important:
- **Secure data access**: Ensures that only authorized users can access verified data.
- **Efficiency and Compliance**: Reduces manual verification overhead while ensuring compliance with security policies.

### Example:
A **Banking System** queries the **Individual Registry** to retrieve verified KYC details of a customer before opening an account. The process works as follows:
1. The **/registry/ekyc/request** API triggers a consent-based verification process.
2. Once authorization is granted, the **/registry/ekyc/data** API returns verified details, digitally signed for integrity.

## Key Design Principles For Registries

### Domain-Driven Design (DDD)

- **Focus on Domain Entities**: Understand the core entities of the registry (e.g., land parcels for a land registry, assets for an asset registry).
- **Use Bounded Contexts**: Encapsulate each registry's business logic and ensure separation of concerns.

### Data Modeling

- **Identify and Design Schemas for Entities**: Ensure that entities are well-defined and can be easily understood.
- **Support Hierarchical or Relational Structures**: Design relationships between entities (e.g., land parcels belonging to a region) for effective data organization.

### Unique Identity Management

- **Assign Unique Identifiers to Every Entity**: Use non-system-generated identifiers to ensure consistency.
- **Use Globally Unique IDs**: Utilize universally unique identifiers (e.g., UUID) or domain-specific identifiers (e.g., Land Parcel Number).

### Data Integrity

- **Enforce Data Validation Rules**: Ensure accurate and consistent information through validation mechanisms.
- **Use Transactions**: Ensure data consistency during updates by using transaction management.

### Traceability & Audit Logs

- **Maintain Immutable Records for All Changes**: Track all changes through event sourcing or audit logs.
- **Track Metadata**: Record essential information like timestamps, user IDs, and reasons for changes.
- **Search History Data**: Provide an API to search through historical data for auditing or traceability purposes.

### Security & Access Control

- **Implement Robust Authentication and Authorization**: Use secure mechanisms to authenticate users and verify access rights.
- **Use Role-Based Access Control (RBAC)**: Restrict access to data based on user roles for improved security.

### Interoperability & Standards

- **Use Standard Data Formats**: For data exchange, support standard formats such as JSON, XML, or CSV.
- **Adhere to Domain-Specific Standards**: Follow established standards like **ISO 19152** for land registries to ensure compatibility with other systems.

### Scalability

- **Design for Scalability**: Ensure that the registry can handle increasing data and user demands.
- **Use Distributed Databases or Sharding**: Leverage scalable databases or partitioning techniques to manage large datasets effectively.

### APIs and Event-Driven Architecture

- **Provide REST or GraphQL APIs**: Expose CRUD and search operations for external integration.
- **Support Event-Driven Architecture**: Notify external systems about changes via events, ensuring seamless integration.

### Loosely Coupled

- **Independent Operation**: A registry should be capable of running independently from other systems.
- **Flexible Integration**: The registry should allow integration with other registries to be turned on or off as needed.

## Key Challenges in Implementing Government Data Registries

### 1. Lack of Unique Identifiers for Records
It is challenging to define a unique identifier for a record, and even if one is defined, older records may not have it, or even new records might face inconsistencies.

**Example**: 
- In an **Individual Registry**, a mobile number was chosen as a unique identifier, but the Punjab government stated that two citizens could share the same number. Later, a **National ID** was chosen instead, but when the registry was deployed in Mozambique, it was found that citizens did not have national IDs at all.
- Sometimes government policies do not allow capturing national IDs, such as with birth certificates.

### 2. Soft References Instead of Hard References Across Registries
Registries should enforce hard links to related records, but due to missing legacy data, they often have to rely on soft references.

**Example**: 
- A **Water Connection Registry** was designed to require a Land Identification Number, but in Punjab, older land records lacked this information. As a result, the requirement was made optional, leading to inconsistent data.

### 3. Non-Validated & Non-Verified Existing Data
Many government registries contain historical, incorrect, or unverified records, making it difficult to ensure accuracy.

**Example**: 
- In a **Property Registry**, many plots had incorrect ownership details, missing measurements, or outdated land use information because no validation was enforced at the time of entry. Similarly, **Business Registries** contained duplicate entries due to different spellings of the same name.

### 4. Cost of Verification for Verifiable Credentials (VCs)
Registries serve as the single source of truth, but issuing Verifiable Credentials requires expensive manual verification of attributes.

**Example**: 
- A **Property Registry** stores information on plot size, construction area, and building type, but to issue a digitally verifiable document, an engineer must physically survey the property. This process is costly and time-consuming, limiting the feasibility of verification for all properties.

### 5. Interoperability Between Old & New Systems
Many government registries run on legacy systems that were not designed for integration with modern digital services.

**Example**: 
- A **Vehicle Registry** in an Indian state was still operating on a mainframe-based database, making it extremely difficult to integrate with the modern Digital License and Taxation System. Developers had to build custom middleware to bridge the gap.

### 6. Security & Privacy Risks
Centralized government registries are high-value targets for cyberattacks, data breaches, and fraud.

**Example**: 
- A **Citizen Registry** containing sensitive personal data was breached, exposing millions of names, addresses, and identification numbers. Similarly, officials in some areas have been found tampering with land records to illegally modify ownership details.

### 7. Lack of Digital Adoption Among Government Officials & Citizens
Many government employees and citizens prefer traditional paper-based records due to a lack of digital literacy or trust in digital systems.

**Example**: 
- When a **Land Registry** was digitized, officials still demanded physical copies of ownership documents for processing, defeating the purpose of the digital transformation. Similarly, in rural areas, citizens often refused to accept digital certificates without an official government stamp.

## Approach to Implementing Registries with Microservices

### Approach 1: Separate Microservices for Each Registry
Each registry (e.g., Property, Trade License, Water Connection) is implemented as a distinct microservice.

#### Pros:
- **High Maintainability & Scalability**: Independent deployment and scaling per registry.
- **Domain-Driven Design (DDD)**: Clear bounded contexts ensure domain logic encapsulation.
- **Loose Coupling**: Services can evolve independently, supporting federated integrations.
- **Security & Access Control**: Fine-grained role-based access control (RBAC) per registry.
- **Performance Optimization**: Services can be optimized based on specific data access patterns.

#### Cons:
- **Increased Complexity**: Multiple codebases and deployment pipelines to maintain.
- **Higher Infrastructure Costs**: Separate deployments require more resources.
- **Inter-service Communication Overhead**: Increased network calls for cross-registry interactions.
- **Duplication of Common Logic**: Common functionalities like auditing, security, and logging may be duplicated.

### Approach 2: Common Registry Service
Create a single, generic registry service capable of managing different registries by storing flexible JSON schemas.

#### Pros:
- **Rapid Development**: Adding new registries is faster as it involves defining a new JSON schema rather than building a new service.
- **Reduced Infrastructure Costs**: Single deployment for all registries reduces resource utilization.
- **Consistency & Reuse**: Common functionalities like auditing, security, and search are implemented once and reused across all registries.
- **Flexibility**: Easy to adapt to new requirements by updating JSON schemas.

#### Cons:
- **Compromised Domain-Driven Design**: Difficult to encapsulate domain logic leading to blurred bounded contexts.
- **Complex Validation & Data Integrity**: Implementing complex validation rules for different domains is challenging.
- **Scalability Issues**: A monolithic database structure may face scaling challenges with increasing data volume.
- **Performance Bottlenecks**: Complex queries due to dynamic schemas may impact performance.
- **Limited Type Safety**: JSON schema lacks strict type enforcement, increasing the risk of data inconsistencies.
- **Single Point of Failure**: 
  - If the common registry service has a bug, it affects all registries (e.g., a validation issue in the schema engine could block new records across all registries).
  - High dependency on this one service; downtime impacts all registry functions.
- **Complex Codebase & Logic**: 
  - The service must support multiple registry schemas, making the code complex.
  - Schema-driven validation, storage, and query handling add complexity.
  - Debugging issues can be harder because errors could originate from schema misconfigurations rather than core logic.

### Recommendations

#### When to Use Separate Microservices:
- If strong domain boundaries, scalability, and maintainability are priorities.
- When complex domain logic and data integrity rules are involved.
- If federated and loosely coupled integrations are essential.

#### When to Use a Common Registry Service:
- If rapid prototyping or proof of concept is needed.
- For registries with simple CRUD operations and minimal business logic.
- When infrastructure cost optimization is critical.

#### Hybrid Approach:
- Use a **common registry** for simple, less critical domains.
- Implement **separate microservices** for complex, business-critical domains.
- Implement a **shared library/module** for common functionalities like auditing, security, and logging.

