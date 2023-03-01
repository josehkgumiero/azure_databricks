# Authentication and authorization
- This section describes concepts that you need to know when you manage azure databricks identities and their access to azure databricks assets.

# User
- A unique individual who has access to the system. User identities are represented by email addresses.

# Service principal
- A service identity for use with jobs, automated tools, and systems such as scipts, apps, and CI/CD platforms. Service pricipals are represented by an application ID.

# Group
- A collection of identities. Grous simplify identity mangament, making it easier to assign access to workspace, data, and other securable objects. All databricks identities can be assigned as members of groups.

# Access control list
- A list of permissions attached to the workspace, cluster, job, table, or experiment. An ACL specifies which users of system processes are granted accss to the objects, as wall as what operations are allowed on the assets. Each entry in a typical ACL specifies a subject and an operation.

# Personal access token
- An opaque string is used to authenticate to the REST API and by tools in the databricks integrations to conect to sql warehouses.
- Azure acive directory tokens can be also be used to authenticate to the REST API.