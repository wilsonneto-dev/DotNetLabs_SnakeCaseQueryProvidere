```
using Microsoft.AspNetCore.Mvc.ModelBinding;
using System.Globalization;

namespace FC.Codeflix.Catalog.API.Configurations;

public class SnakeCaseQueryProvider : QueryStringValueProvider, IValueProvider
{
    public SnakeCaseQueryProvider(
        BindingSource bindingSource,
        IQueryCollection values,
        CultureInfo culture)
        : base(bindingSource, values, culture)
    {
    }

    public override bool ContainsPrefix(string prefix)
    {
        return base.ContainsPrefix(prefix.ToSnakeCase());
    }

    public override ValueProviderResult GetValue(string key)
    {
        return base.GetValue(key.ToSnakeCase());
    }
}

public class SnakeCaseQueryValueProviderFactory : IValueProviderFactory
{
    public Task CreateValueProviderAsync(ValueProviderFactoryContext context)
    {
        if (context == null)
        {
            throw new ArgumentNullException(nameof(context));
        }

        var valueProvider = new SnakeCaseQueryProvider(
            BindingSource.Query,
            context.ActionContext.HttpContext.Request.Query,
            CultureInfo.CurrentCulture);

        context.ValueProviders.Add(valueProvider);

        return Task.CompletedTask;
    }
}

```

```
options.ValueProviderFactories.Add(new SnakeCaseQueryValueProviderFactory());
```

