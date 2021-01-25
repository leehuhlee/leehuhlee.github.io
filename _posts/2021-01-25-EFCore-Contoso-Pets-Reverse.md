---
layout: post
title: "[Entity Framework Core] Contoso Pets Reverse"
date: 2021-01-25
excerpt: "Entity Famework Core"
tags: [C#, DotNet, Entity Framework Core]
comments: false
---

<center>Reference by <a href="https://channel9.msdn.com/Series/Entity-Framework-Core-101">Entity Framework Core 101</a></center>


## NuGet Packages
* Microsoft.EntityFrameworkCore.Design
 - Shared design-time components for Entity Framework Core tools.
<figure>
  <a href="/assets/img/posts/efcore_contosopets_reverse/1.jpg"><img src="/assets/img/posts/efcore_contosopets_reverse/1.jpg"></a>
	<figcaption>Entity Framework Core Contoso Pets</figcaption>
</figure>


## Package Manager Console
* Scaffold-DbContext
  - Generates code for a DbContext and entity types for a database. 
  - In order for Scaffold-DbContext to generate an entity type, the database table must have a primary key.
{% highlight C# %}
  Scaffold-DbContext "Data Source=(localdb)\MSSQLLocalDB;initial catalog=ContosoPets;Integrated Security=True;ConnectRetryCount=0" 
  Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Context CCIPContext  -DataAnnotations
{% endhighlight %}
* ConnectRetryCount
  - The number of times the driver retries connection attempts to the primary database server, and if specified, alternate servers until a successful connection is established.
  - This option and the Connection Retry Delay connection option, which specifies the wait interval between attempts, can be used in conjunction with failover.
{% highlight C# %}
  "ConnectRetryCount=0;"
{% endhighlight %}
* -Provider <String>
  - The provider to use. 
  - Typically this is the name of the NuGet package, for example: Microsoft.EntityFrameworkCore.SqlServer. 
  - This is a positional parameter and is required.
{% highlight C# %}
  Microsoft.EntityFrameworkCore.SqlServer
{% endhighlight %}
* -OutputDir <String>
  - The directory to put files in. 
  - Paths are relative to the project directory.
{% highlight C# %}
  -OutputDir Models
{% endhighlight %}
* -Context <String>
  - The name of the DbContext class to generate.
{% highlight C# %}
  -Context CCIPContext
{% endhighlight %}
* -DataAnnotations
  - Use attributes to configure the model (where possible). If this parameter is omitted, only the fluent API is used.
{% highlight C# %}
  -DataAnnotations
{% endhighlight %}


<figure class="third">
  <a href="/assets/img/posts/efcore_contosopets_reverse/0.jpg"><img src="/assets/img/posts/efcore_contosopets_reverse/0.jpg"></a>
  <a href="/assets/img/posts/efcore_contosopets_reverse/2.jpg"><img src="/assets/img/posts/efcore_contosopets_reverse/2.jpg"></a>
  <a href="/assets/img/posts/efcore_contosopets_reverse/3.jpg"><img src="/assets/img/posts/efcore_contosopets_reverse/3.jpg"></a>
	<figcaption>Entity Framework Core Contoso Pets Reverse</figcaption>
</figure>


## Result
<figure>
  <a href="/assets/img/posts/efcore_contosopets_reverse/4.jpg"><img src="/assets/img/posts/efcore_contosopets_reverse/4.jpg"></a>
	<figcaption>Entity Framework Core Contoso Pets Reverse</figcaption>
</figure>

[Download](https://github.com/leehuhlee/CShap){: .btn}
