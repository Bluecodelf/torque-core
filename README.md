# Torque Framework (core)

Torque is a **toy project** that explores the possibility of an ABI that allows
applications to expose and consume REST-based APIs that will automatically be
optimized for the target system.

### Example use case

Let's imagine that we have a simple project management application. The
backend for this project management application relies on two other backends:
- One being a user referential/database. This backend will take care of account
creation, management, but also the authentication flow and permission system
for every other application in the system. We will call this backend `B1`.
- The other one is a project referential/database. It also interacts with the
previous backend as it automatically creates the appropriate permission groups
for new projects: administrators, developers, business analysts... We will call
this backend `B2`.

Now, let's use the example of a request from management: they asked us to create
an endpoint that would allow us to create a new project (that we will call `P2`)
related to an existing project (that we will call `P1`).

Internally, our project management backend will implement this use case like
this:
- First, we call `B1` to make sure that we are authenticated and to check if we
have the necessary permissions for creating a project *and* for linking `P2` to
`P1`. If not, we respond a `403 Bad Request`.
- Then, we call `B2` to create the new project. The backend will, in turn, call
`B1` to create the appropriate permission groups.
- Depending on our APIs, we might call `B2` again to bulk-edit `P1` tickets and
link them to `P2`.
- Finally, we might forward the user to `P2` project page, and call both `B1`
and `B2` to check permissions again, and retrieve the newly created project's
metadata.

This is a lot of REST calls! Which will surely not be an issue for a big company
with a lot of network and computing resources, but might be problematic for a
startup that has `B1`, `B2` and the project management backend all running on
the same server.

To optimize these calls, we could - for example - run the HTTP requests through
an Unix socket, or use a binary protocol easier to parse for low/medium-end
servers, but this is difficult to do without editing the code of the underlying
backends. Torque explores the possibility of doing that automatically by
wrapping those calls accordingly, and thus reducing operating costs for low/mid
range servers, or cloud deployments.