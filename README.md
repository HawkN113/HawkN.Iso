# HawkN.Iso
`HawkN.Iso` exists to eliminate string-based representations of international standards from business logic.

[![NuGet Trusted Publishing](https://img.shields.io/badge/NuGet-Trusted_Publishing-blue?logo=nuget&logoColor=white&style=flat-square)](https://learn.microsoft.com/en-us/nuget/nuget-org/trusted-publishing)
[![GitHub license](https://img.shields.io/badge/license-MIT-green.svg?label=License&style=flat-square)](LICENSE)
[![Data: Unicode CLDR](https://img.shields.io/badge/data-Unicode%20CLDR-lightgrey.svg?label=Data%20license&style=flat-square)](https://unicode.org/license.html)
[![Data: ODbL](https://img.shields.io/badge/data-ODbL%201.0-orange.svg?label=Data%20license&style=flat-square)](https://opendatacommons.org/licenses/odbl/1-0/)
> **A strongly-typed reference framework for ISO and CLDR-based standards in .NET**

`HawkN.Iso` is a modular, strongly-typed framework that provides **canonical representations of international standards** (ISO, UN, CLDR) for the .NET ecosystem.

This is **not a utility library**.  
This is a **reference layer** designed to eliminate string-based, error-prone representations of standardized data.

---

## 🎯 Design Goals

- ✅ Strong typing over strings
- ✅ Immutable domain objects
- ✅ Deterministic behavior
- ✅ Compile-time safety
- ✅ No runtime parsing
- ✅ AOT-friendly
- ✅ Explicit, documented data sources

---

## 🚫 What HawkN.Iso Is NOT

- ❌ Not a localization framework
- ❌ Not a formatting or UI library
- ❌ Not a database or external API client
- ❌ Not an enum dump
- ❌ Not a replacement for .NET BCL globalization APIs

> `HawkN.Iso` provides **canonical data models**, not presentation or localization logic.

---

## 📦 Modules

| Module                                                                                                                                                                                                                                                                                                                                                                                | Standard | Description | Project link                                                           |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|------------|------------------------------------------------------------------------|
| [![NuGet](https://img.shields.io/nuget/v/HawkN.Iso.Countries?label=HawkN.Iso.Countries&color=blue&style=flat-square)](https://www.nuget.org/packages/HawkN.Iso.Countries/)[![Downloads](https://img.shields.io/nuget/dt/HawkN.Iso.Countries?label=&logo=nuget&color=brightgreen&style=flat-square&cacheSeconds=3600)](https://www.nuget.org/packages/HawkN.Iso.Countries/)            | ISO 3166 | Compile-time safe ISO 3166 country representations for business logic | [HawkN.Iso.Countries](https://github.com/HawkN113/HawkN.Iso.Countries) |
| [![NuGet](https://img.shields.io/nuget/v/HawkN.Iso.Currencies?label=HawkN.Iso.Currencies&color=blue&style=flat-square)](https://www.nuget.org/packages/HawkN.Iso.Currencies/)[![Downloads](https://img.shields.io/nuget/dt/HawkN.Iso.Currencies?label=&logo=nuget&color=brightgreen&style=flat-square&cacheSeconds=3600)](https://www.nuget.org/packages/HawkN.Iso.Currencies/)       | ISO 4217 | Strongly-typed currency definitions | [HawkN.Iso.Currencies](https://github.com/HawkN113/HawkN.Iso.Currencies)                                                                     |
| [![NuGet](https://img.shields.io/nuget/v/HawkN.Iso.Countries.Currencies?label=HawkN.Iso.Countries&color=blue&style=flat-square)](https://www.nuget.org/packages/HawkN.Iso.Countries.Currencies/)[![Downloads](https://img.shields.io/nuget/dt/HawkN.Iso.Countries.Currencies?label=&logo=nuget&color=brightgreen&style=flat-square&cacheSeconds=3600)](https://www.nuget.org/packages/HawkN.Iso.Countries.Currencies/) | CLDR | Canonical country → currency mapping | [HawkN.Iso.Countries](https://github.com/HawkN113/HawkN.Iso.Countries)                                                                     |

Each module is versioned independently and can be used standalone.

---

## 🧩 Example

### Registration
- Use extension method `.AddCountryCodeService();` for countries
- Use extension method `.AddCurrencyService();` for currencies
```csharp
using var host = Host.CreateDefaultBuilder(args)
    .ConfigureServices(services =>
    {
        services.AddCountryCodeService();
        services.AddCurrencyService();
    })
    .Build();
```
### How to use
To get service instances:
```csharp
var currencyService = scope.ServiceProvider.GetRequiredService<ICurrencyService>();
var countryCodeService = scope.ServiceProvider.GetRequiredService<ICountryCodeService>();
```
or inject
```csharp
app.MapGet("/weatherforecast", ([FromServices] ICurrencyService currencyService, ICountryCodeService countryCodeService) => ...
````

The service provides O(1) lookups via pre-indexed dictionaries and efficient partial searching.
```csharp
var countryCodeService = scope.ServiceProvider.GetRequiredService<ICountryCodeService>();
var currencyService = scope.ServiceProvider.GetRequiredService<ICurrencyService>();

// Get all countries sorted by name
var countries = countryCodeService.GetAll();

// Lookup by string (Supports Alpha-2, Alpha-3, or Numeric)
var germany = countryCodeService.FindByCode("DE");
var austria = countryCodeService.FindByCode("040");

// Get all currencies
var currencies = currencyService?.Query()
    .Includes
        .Type(CurrencyType.Fiat)
        .Type(CurrencyType.SpecialUnit)
        .Type(CurrencyType.SpecialReserve)
        .Type(CurrencyType.PreciousMetal)
   .Build();
```
### Currency mapping
```csharp
var country = CountryCode.TwoLetterCode.CH;

// Primary currency
var primary = country.GetPrimaryCurrency();   // CHF

// Secondary currencies
var secondary = country.GetSecondaryCurrencies();

// All currencies
var all = country.GetAllCurrencies();

// Check if used
bool usesChe = country.IsCurrencyUsedByCountry(CurrencyCode.CHE);

// Check if used the currency
bool usesChe = country.IsCurrencyUsedByCountry(CurrencyCode.CHE);
```

- No strings.
- No magic values.
- No runtime lookups.

---

## 🧠 Philosophy

> Standards are data — not strings.

If a value is defined by an international standard, it should be:
- represented explicitly,
- immutable by design,
- validated at compile time,
- safe to use in business logic.

`HawkN.Iso` focuses on **correctness, clarity, and long-term stability**.

## 🧱 Architecture Principles
- Explicit domain boundaries
- No hidden dependencies between standards
- No localization or formatting concerns
- No reflection-heavy runtime logic
- Designed for source generators and AOT scenarios

---

## 🔮 Roadmap (High-level)
Planned and potential modules:
- `HawkN.Iso.Languages` — ISO 639
- `HawkN.Iso.Regions` — UN M.49

The roadmap is intentionally conservative to preserve long-term API stability.

---

## 📜 License

### Code
This project’s source code is licensed under the [MIT License](LICENSE).

### Data
This project uses data derived from the following sources:

- **Unicode Common Locale Data Repository (CLDR)**  
  Licensed under the [Unicode License Agreement](https://unicode.org/license.html).

- Country data (`ISO 3166-1` and `UN M49` numeric codes) is sourced from the [UN Statistics Division – M49 standard](https://unstats.un.org/unsd/methodology/m49/overview)

The above data licenses are **permissive and compatible with MIT-licensed code**  
when used for reference and code generation.

---
