# More Currency Display Choices

## Status

Champion: Eemeli Aro (Mozilla)  
Stage: 2

## Motivation & Use Cases

In JavaScript, localized currency formatting is made possible with Intl.NumberFormat
and its `style: 'currency'` option.

The indication of the currency is controlled by the `currencyDisplay` option,
which currently supports formatting it as
a `symbol` (e.g. "\$" or "US$"), a `narrowSymbol` (e.g. "$"),
an ISO currency `code` (e.g. "USD"), or as a spelled-out `name` (e.g. "US dollars").

For `symbol` formatting, the choice between a "narrow" and "formal" symbol
is made per currency and per locale,
so that e.g. USD values are formatted as "\$" with the en-US locale, but as "US$" with the en-CA locale.

These existing options do not support the following use cases:

- When formatting multiple currencies in the same context (such as a web page),
  it should be possible to differentiate each currency with its full formal symbol,
  using e.g. "US$" for USD and "CA$" for CAD.
  However, it is not possible to format USD as "US$" in the en-US locale,
  as its `symbol` style always formats as "$".
  [[Feature request](https://github.com/tc39/ecma402/issues/642)]

- When formatting currency values in a very narrow space
  or in a setting where the currency symbol or name is provided otherwise,
  it is useful to be able to omit the currency indicator completely
  while still using the currency formatting style to control the number of fraction digits
  (which dependens on the currency).
  The only way to do this now is by formatting to parts,
  and filtering out the currency symbol or code and any adjoining whitespace from the output.
  [[Stack Overflow question](https://stackoverflow.com/q/68549027)]

## Description

The proposed solution is to add the following two `currencyDisplay` option values:

- `formalSymbol` — A wider format symbol or the ISO currency code ("US$100" rather than "$100").
  An alternative spelling for this could be `wideSymbol`.
- `never` — Do not display any currency symbol or name.

```js
const formal = new Intl.NumberFormat("en-US", {
  style: "currency",
  currency: "USD",
  currencyDisplay: "formalSymbol",
});
formal.format(42); // "US$42.00"

const never = new Intl.NumberFormat("en-US", {
  style: "currency",
  currency: "USD",
  currencyDisplay: "never",
});
never.format(42); // "42.00"
```

## Comparison

ICU includes
[FORMAL_SYMBOL_NAME](https://unicode-org.github.io/icu-docs/apidoc/dev/icu4j/com/ibm/icu/util/Currency.html#) /
[UCURR_FORMAL_SYMBOL_NAME](https://unicode-org.github.io/icu-docs/apidoc/dev/icu4c/ucurr_8h.html#a881ffe99944d926413324029c9bd577fa493b81f7c7572db858c1d988b22e73e6)
to provide the same functionality as `formalSymbol`:

> The formal currency symbol is similar to the regular currency symbol, but it always takes the form used in formal settings such as banking; for example, "NT$" instead of "$" for TWD in zh-TW.
