---
key: 20180301
modify_date: 2018-03-03
tags: [Software Design, C#, English]
title: S.O.L.I.D Principles
mermaid: true
mathjax: true
---

**S.O.L.I.D** Principles is also known as **the first five object-oriented design(OOD) principles**, to improve software maintainability and extensibility. Introduced by Uncle Bob (Robert C. Martin), those principles are indispensible for my daily DevOps life.

<!--more-->

# **S.O.L.I.D**

The acronyms stands for:

- S - Single responsibility principle
- O - Open-closed principle
- L - Liskov substitution principle
- I - Interface segregation principle
- D - Dependency inversion principle

Recently I'm getting more actual practices on those principles with real life projects (via the heavy usage of interface), rather than just reading them on textbooks.

## Single responsibility principle

***A class should have one and only one reason to change***, meaning that a class should have only one job to do.
{% highlight C# %}
public class Order
{
    // Some attributes
    public double Total { get; set; } 
}
{% endhighlight %}
{% highlight C# %}
public class Customer
{
    // Some attributes
    public string Membership { get; set; } 
}
{% endhighlight %}
{% highlight C# %}
public class OrderCalculator
{
    public double CalculateFinalTotal(Customer customer, Order order)
    {
        if (customer.Membership.Equals("basic"))
          {
              return order.Total * 1;
          }
          if (customer.Membership.Equals("pro"))
          {
              return order.Total * 0.95;
          }
    }
}
{% endhighlight %}

`CalculateFinalTotal()` is taking up extra responsibility (calculate discount) than it should, we extract the responsibility and assign it to `GetDiscount()`.
{% highlight C# %}
public class OrderCalculator
{
    public double CalculateFinalTotal(Customer customer, Order order)
    {
        double discount = 1 - GetDiscount(customer)
        return order.Total * discount;
    }

    public double GetDiscount(Customer customer)
    {
        if (customer.Membership.Equals("basic"))
          {
              return 0;
          }
          if (customer.Membership.Equals("pro"))
          {
              return 0.1;
          }
    }
}
{% endhighlight %}

But hang on, this leads to the violation of next principle.

## Open-closed principle

***Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification.***

If we add a new `Membership` type `premium` in our customer database, `GetDiscount()` will have to be modified to add another `if` block. 

{% highlight C# %}
public double GetDiscount(Customer customer)
{
    if (customer.Membership.Equals("basic"))
    {
        return 0;
    }
    if (customer.Membership.Equals("pro"))
    {
        return 0.1;
    }
    if (customer.Membership.Equals("premium"))
    {
        return 0.2;
    }
}
{% endhighlight %}

Modifying `GetDiscount()` is clearly against the O, and by doing so we have to ensure all the previous conditions are still working as intended (extra testing workload). This is where interface comes to our rescue. 

{% highlight C# %}
public interface IDiscount
{
    double CalculateDiscount();
}

public class BasicDiscount : IDiscount
{
    public double CalculateDiscount()
    {
       return 0;
    }
}

public class ProDiscount : IDiscount
{
    public double CalculateDiscount()
    {
       return 0.1;
    }
}

public class PremiumDiscount : IDiscount
{
    public double CalculateDiscount()
    {
       return 0.2;
    }
}
{% endhighlight %}

And now we should get something like this:

{% highlight C# %}
public class OrderCalculator
{
    private readonly IDiscount _discount;

    public OrderCalculator(IDiscount discount)
    {
        _discount = discount;
    }

    public double CalculateFinalTotal(Order order)
    {
        var discount = 1 - GetDiscount()
        return order.Total * discount;
    }

    public double GetDiscount()
    {
        return _discount.CalculateDiscount();
    }
}
{% endhighlight %}

This way we won't have to modify `CalculateDiscount()` each time when a new `Customer` type is added. Instead we just implement (extending not modifying) a new `IDiscount` and feed that into our `OrderCalculator`, `OrderCalculator` will return a different result even though its logic is unchanged. In this case, three memberships return different discount rates.

## Liskov substitution principle

> Let $$\phi(x) $$ be a property provable about objects $$x $$  of type $$T$$. Then $$\phi(y) $$ should be true for objects $$y $$ of type $$S$$ where $$S$$ is a subtype of $$T$$.

Liskov substitution principle is a bit trickier to understand. In short, ***the derived class or child class should not break the behaviours of its parent class.***

Now we want to encourage customers to upgrade their membership status by introducing a promotion discount.

We add method `AddPromotionDiscount()` to `IDiscount`:
{% highlight C# %}
public interface IDiscount
{
    double CalculateDiscount();

    double AddPromotionDiscount();
}
{% endhighlight %}

And since `Premium` customers have reached the top tier of our membership system, we do not want to offer them this incentive, so we do not implement `AddPromotionDiscount()` for `PremiumDiscount`.
{% highlight C# %}
public class BasicDiscount : IDiscount
{
    public double CalculateDiscount()
    {
        return 0;
    }

    public double AddPromotionDiscount()
    {
        return 0.05;
    }
}

public class ProDiscount : IDiscount
{
    public double CalculateDiscount()
    {
        return 0.1;
    }
    public double AddPromotionDiscount()
    {
        return 0.05;
    }
}

public class PremiumDiscount : IDiscount
{
    public double CalculateDiscount()
    {
        return 0.2;
    }
    public double AddPromotionDiscount()
    {
        // The upgrade offer is not applicable to premium users
        throw new System.NotImplementedException();
    }
}
{% endhighlight %}

The polymorphic behaviour of interface allows us to do the following:
{% highlight C# %}
var discounts = new List<IDiscount>
{
    new BasicDiscount(),
    new ProDiscount(),
    new PremiumDiscount()
};

foreach (var dp in discounts)
{
    var totalDiscount = dp.CalculateDiscount() + dp.AddPromotionDiscount();
}
{% endhighlight %}

Since `PremiumDiscount` doesn't have the concrete implementation of `AddPromotionDiscount()`, this code snippet is syntactically correct but will still throw an exception during runtime. How do we avoid this? Let's look at [the next principle I](#interface-segregation-principle).

## Interface segregation principle

***No client should be forced to depend on methods it does not use.*** 

This is extremely important for DevOps as every time we ship out a new feature, we try to **avoid modifying the existing ones**. 

Continue from the L, we should again segregate `IDiscount` with their functions:

{% highlight C# %}
public interface IMembershipDiscount
{
    double CalculateDiscount();
}

public interface IPromotionDiscount
{
    double AddPromotionDiscount();
}
{% endhighlight %}

Now we let `PremiumDiscount` to implement `IMembershipDiscount` only, the rest should have implemented both `IMembershipDiscount` and `IPromotionDiscount`:

{% highlight C# %}
public class BasicDiscount : IMembershipDiscount, IPromotionDiscount
{
    public double CalculateDiscount()
    {
        return 0;
    }

    public double AddPromotionDiscount()
    {
        return 0.05;
    }
}

public class ProDiscount : IMembershipDiscount, IPromotionDiscount
{
    public double CalculateDiscount()
    {
        return 0.1;
    }
    public double AddPromotionDiscount()
    {
        return 0.05;
    }
}

public class PremiumDiscount : IMembershipDiscount
{
    public double CalculateDiscount()
    {
        return 0.2;
    }
}
{% endhighlight %}

A graphical illustration of what's going on here:
{% raw %}
<!-- markdownlint-disable MD033 -->
<p class="language-mermaid">
graph LR;
    Basic(BasicDiscount)
    Pro(ProDiscount)
    Premium(PremiumDiscount)
    Membership("CalculateDiscount()")
    Promotion("AddPromotionDiscount()")

    subgraph IMembershipDiscount
      subgraph IPromotionDiscount
       Basic
       Pro
      end
      Premium
    end

    subgraph Funtions
      Membership
      Promotion
    end

    Basic-->Membership;
    Pro-->Membership;
    Premium-->Membership;

    Basic-->Promotion;
    Pro-->Promotion;
</p>
{% endraw %}

And now your IDE should happily point out your syntax error, since `PremiumDiscount` has never implemented `IPromotionDiscount` thus has no `CalculateDiscount()` method.
{% highlight C# %}
var discounts = new List<IPromotionDiscount>
{
    new BasicDiscount(),
    new ProDiscount(),
    new PremiumDiscount() // here
};

foreach (var dp in discounts)
{
    var totalDiscount = dp.CalculateDiscount() /* and here */  
                      + dp.AddPromotionDiscount();
}
{% endhighlight %}

`AddPromotionDiscount()` is an useless method for `PremiumDiscount`, therefore we shouldn't force it onto `PremiumDiscount`. By segregating `IDiscount` into two interfaces, we conform to the I. This structure now also satisfies [the L](#liskov-substitution-principle).

## Dependency inversion principle

1. ***High-level modules should not depend on low-level modules. Both should depend on abstractions.***
2. ***Abstractions should not depend on details. Details should depend on abstractions.***

As the project grows larger and larger, it will become extremely difficult to maintain if we don't decouple them properly, a small change in one base class could lead to a massive overhaul of its derived classes. We should make sure our business logics only depend on abstractions.

One example is the use of database. We often run into the need of switching database (like from MySQL to PostgreSQL), it could have been a nightmare but we followed the D and used interface for our database logic.

{% highlight C# %}
public interface IDbRepository : IDisposable
{
    ProductInfo GetProductInfoById(int id);
    void InsertProductInfo(Product product);
    void UpdateProductInfo(Product product);
    void AddOrUpdate(IEnumerable<Product> products);
}
{% endhighlight %}

We have clearly defined the methods needed for our database operation, and switching to another database will almost certainly change the query syntax. If that happens, we simply implement another `IDbRepository`.

{% highlight C# %}
public class PostgresRepository : IDbRepository
{
    private ProductInfoContext _dbContext;

    public PostgresRepository(ProductInfoContext context)
    {
        _dbContext = context;
    }

    // concrete implementations of all methods here
}
{% endhighlight %}

Since everything is based on the abstraction, it ensures that we don't rely on a single provider of service (like SMS and OAuth) and have the ability to switch between providers quickly without interrupting other parts of the application. This to a certain extent also conforms to [the S](#open-closed-principle).

# Conclusion

Because the flexibility interface provides, we use interface heavily during development to comply with S.O.L.I.D principles.  

Another beauty of interface is that, they are nothing but a bunch of method definitions, there is no concrete implementation but they provide a set of clear guidelines on input/output. The test team is able to test the application before we even finished developing our logic.

Following S.O.L.I.D we make our code a lot easier to understand, maintain, expand, test etc. For DevOps this means dramatically reduction off development time needed yet we deliver better product quality.
