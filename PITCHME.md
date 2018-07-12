#### Versioning and Maintaining Your REST API in ASP.NET Core

##### Spencer Schneidenbach

@schneidenbach

---

Twitter @schneidenbach

## Slides plus more at

rest.schneids.net

---

![](assets/boss.jpg)

"I want a new API"

---

![](assets/boss.jpg)

"I want a new REST API"

---

![](assets/newproject.png)

---

## Endpoint structure?

---

## Documentation?

---

![](assets/boss.jpg)

"Update existing REST API"

---

## Extending Endpoints?

---

## Changing existing behaviors?

---

![](assets/angry.jpg)

---

## Versioning and Maintaining

---

# Versioning

---

## "Typical" REST URLs

GET `api/Jobs`  
GET `api/Jobs/12345`  
PUT `api/Jobs/12345`  
POST `api/Jobs`

---

## "Typical" REST URLs

GET `api/Jobs`  
GET `api/Jobs/12345`  
PUT `api/Jobs/12345`  
POST `api/Jobs` NEEDS TO CHANGE?

---

## Versioning to the rescue

---

## Version formats

* /api/foo?api-version=1.0
* /api/foo?api-version=2.0-Alpha
* /api/foo?api-version=2015-05-01.3.0
* /api/v1/foo
* /api/v2.0-Alpha/foo
* /api/v2015-05-01.3.0/foo

https://github.com/Microsoft/aspnet-api-versioning/wiki/Version-Format

---

Truth is, you don't need 'em

---

## Nuget

Microsoft.AspNetCore.Mvc.Versioning

---

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.AddApiVersioning();
}
```

---

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.AddApiVersioning();
}
```
Query string by default

---

```csharp
namespace ConstructionApp.Jobs.V1
{
  [ApiVersion("1.0")]
  [Produces("application/json")]
  [Route("api/jobs")]
  public class JobsController : Controller {}
}

namespace ConstructionApp.Jobs.V2
{
  [ApiVersion("2.0")]
  [Produces("application/json")]
  [Route("api/jobs")]
  public class JobsController : Controller {}
}
```

---

```csharp
services.AddApiVersioning(o =>
{
    o.ApiVersionReader = new HeaderApiVersionReader("X-Api-Version");
});
```

`GET` /api/Jobs HTTP/1.1  
Host: localhost  
X-Api-Version: 2.0

---

```csharp
services.AddApiVersioning(o =>
{
    o.ApiVersionReader = new QueryStringApiVersionReader("version");
});
```

`GET` /api/Jobs?version=2.0 HTTP/1.1  
Host: localhost  

---

```csharp
services.AddApiVersioning(o =>
{
    o.ApiVersionReader = new UrlSegmentApiVersionReader();
});
```

```csharp
namespace ConstructionApp.Jobs.V1
{
  [ApiVersion("1.0")]
  [Produces("application/json")]
  [Route("api/v{version:apiVersion}/jobs")]
  public class JobsController : Controller {}
}
```

---

```csharp
services.AddApiVersioning(o =>
{
    o.ApiVersionReader = new UrlSegmentApiVersionReader();
});
```

`GET` /api/v2.0/Jobs HTTP/1.1  
Host: localhost  

---

```csharp
services.AddApiVersioning(o =>
{
    o.ApiVersionReader = new MediaTypeApiVersionReader();
});
```

`GET` /api/Jobs HTTP/1.1  
Host: localhost  
Accept: application/json;v=1.0

---

```csharp
services.AddApiVersioning(o =>
{
    o.ApiVersionReader = new MediaTypeApiVersionReader();
});
```

`POST` /api/Jobs HTTP/1.1  
Host: localhost  
Content-Type: application/json;v=1.0

---

```csharp
services.AddApiVersioning(o =>
{
    o.ApiVersionReader = ApiVersionReader.Combine(
        new QueryStringApiVersionReader(),
        new HeaderApiVersionReader
        {
            HeaderNames = {"api-version"}
        });
});
```

---

Adding versions to existing APIs

---

```csharp
services.AddApiVersioning(o =>
{
    o.AssumeDefaultVersionWhenUnspecified = true;
    //default version is 1.0
});
```

Add to a brownfield project

---

```csharp
namespace ConstructionApp.Jobs.V1
{
  [ApiVersion("1.0")]   //this becomes optional
  [Produces("application/json")]
  [Route("api/jobs")]
  public class JobsController : Controller {}
}
```

`GET` /api/jobs HTTP/1.1

---

```csharp
namespace ConstructionApp.Jobs.V1
{
  [Produces("application/json")]
  [Route("api/jobs")]
  public class JobsController : Controller {}
}
```

`GET` /api/jobs?version=1.0 HTTP/1.1

---

```csharp
services.AddApiVersioning(o =>
{
    o.AssumeDefaultVersionWhenUnspecified = true;
    o.DefaultApiVersion = new ApiVersion( new DateTime( 2018, 7, 1 ) );
});
```

---

# Swagger?

---

# Swagger?

https://github.com/Microsoft/aspnet-api-versioning/wiki/Swashbuckle-Integration

---

The real question

---

Do you need this?

---

```csharp
namespace ConstructionApp.Jobs.V1
{
  [Produces("application/json")]
  [Route("api/v1/jobs")]
  public class JobsController : Controller {}
}

namespace ConstructionApp.Jobs.V2
{
  [Produces("application/json")]
  [Route("api/v2/jobs")]
  public class JobsController : Controller {}
}
```

---

Simple/easy to understand

---

Lesson: Introduce as much complexity as you need

---

![Stripe logo](assets/stripe.png)

---

## Takeaways

Do it up front when possible

---

## Takeaways

Versioning most common in URL

---

## Takeaways

Keep it simple when possible

---

## Takeaways

Document Document Document

---

### Lots more options

https://github.com/Microsoft/aspnet-api-versioning/wiki

---

Version strategy chosen

---

How to maintain?

---

Application lifecycle question

---

![](assets/break-contract.jpg)

---

# RULE

Don't break contracts. Ever.

---

`GET` /api/v2.0/Jobs HTTP/1.1  
Host: localhost  

```json
{
    "name": "Building a Wal-Mart",
    "code": "12345-67",
    "address": "123 Main Street"
}
```

---

`GET` /api/v2.0/Jobs HTTP/1.1  
Host: localhost  

```json
{
    "name": "Office buildout",
    "code": "12345-67",
    "address": "123 Main Street",
    "address2": "Suite 17"
}
```

---

`GET` /api/v2.0/Jobs HTTP/1.1  
Host: localhost  

```json
{
    "name": "Building a Wal-Mart",
    "code": "12345-67"
}
```

---

# RULE

Don't break contracts. Ever.

---

# RULE

Don't break contracts. Ever.  

...is ideal

---

# RULE

Don't break contracts. Ever.  

...is just not practical

---

Who/What/Where/When/Why

---

# Why?

---

# Why

```json
{
    "name": "Office buildout",
    "code": "12345-67",
    "address": "123 Main Street",
    "address2": "Suite 17"
}
```

---

# Who

...is your consumer?  
* Internal?
* External (integration)?

---

# What

...needs changing?

---

# Where

...are you in development?

---

![Mario vs Mario](assets/mario.jpg)

---

### Timeline

* March 2018 - POC completed
* May 2018 - Return objects solidified
* July 2018 - QC passed

---

# When?

---

# When?

Hotfix? End of sprint?

---

## What is your versioning strategy?

---

## Versions are a balancing act

---

# Version 2.0

```json
{
    "name": "Office buildout",
    "code": "12345-67",
    "address": "123 Main Street",
    "address2": "Suite 17"
}
```

---

# Version 3.0

```json
{
    "name": "Office buildout",
    "code": "12345-67",
    "address": "123 Main Street",
    "address2": "Suite 17",
    "city": "Kirkwood"
}
```

---

# Version 56.0

```javascript
{
    "name": "Office buildout",
    "code": "12345-67",
    "address": "123 Main Street",
    "address2": "Suite 17",
    "city": "Kirkwood",
    /* ...100 more properties... */
}
```

---

![](assets/yoda.jpg)

---

### Deprecation is a good strategy

---

```csharp
namespace ConstructionApp.Jobs.V1
{
  [Produces("application/json")]
  [Route("api/v1/jobs")]
  public class JobsController : Controller {}
}
```

---

```csharp
namespace ConstructionApp.Jobs.V1
{
  [Produces("application/json")]
  [Route("api/v1/jobs")]
  public class JobsController : Controller {}
}
```

#### Response includes:  
api-deprecated-versions: 1.0

---

## Takeaways

Break minimally

---

## Takeaways

Remember app lifecycle

---

## Takeaways

Fix bugs, but... break minimally

---

## Takeaways

Break minimally

---

The bottom line is that we have to build

---

### More resources

rest.schneids.net

---

## Thank you!

[@schneidenbach](https://twitter.com/schneidenbach)  
schneids.net
