# Choosing a ServiceLifetime
A subtle source of errors and less-than-subtle source of frustration is understanding and using service lifetimes appropriately with .NET Core dependency injection. Service lifetimes, while complicated on the surface, can help developers that need to share state across different lifetimes of an application. Typically, a long-running application has three service lifetimes that we want a service to "live" for or be shared:

- One instance shared for the lifetime of the application
- One instance shared for some request/action/activity/unit of work
- I don't care/it doesn't matter/why are you asking me

Without dependency injection, we'd typically attack these by:

- Create one static/shared instance
- Create one instance then pass it through to everything in the request/action/activity/unit of work
- Just `new` it up whenever

If I'm using a framework that leverages a dependency injection container, I'll need to instead conform to the container's rules about lifetimes and avoid trying to use the "old" ways. In the .NET Core container, these three service lifetimes map to:

- `ServiceLifetime.Singleton`
- `ServiceLifetime.Scoped`
- `ServiceLifetime.Transient`

Typically, your application won't actually create scopes itself, and instead that's done by some middleware you don't interact with (a good thing).

So why is it so easy to get wrong? In my experience fielding MANY questions on GitHub, it all comes down to how we should decide which lifetime to use. Most mistakes come from premature optimization, and I've been guilty of this as well.

## Choosing a `ServiceLifetime`

Going back to the three typical lifetimes before dependency injection, the reason a service would need a different lifetime is quite simple: State

For an injected service, my lifetime choice happens at registration time, when I'm connecting the service to its implementation. It's the implementation's state that determines the lifetime. When I refer to state, I don't mean merely the fields on the class - as the fields could be other services. I refer to "State" as "data" or information, and that scope in which that data or information needs to be shared determines the `ServiceLifetime`:

![chart](./choosing-a-service-lifetime/img123.png)

