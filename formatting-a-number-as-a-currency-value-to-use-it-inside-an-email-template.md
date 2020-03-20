# Formatting a number as a currency value to use it inside an email template

In order to convert a number to a currency value, the following function can be used:

```javascript
utils.formatNumber(number, showDecimals, thousandSeparator, decimalsNumber, decimalSeparator)
```

* number: number to format as a currency
* showDecimals true to show decimals, false to hide them
* thousandSeparator: grouping separator; e.g. ‘,’
* decimalsNumber the number of decimal digits to show
* decimalSeparator: the separator to use between decimal and integer parts; e.g. ‘.’

