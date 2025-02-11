# Validation Rules
Validation rules can be specified as an array containing items in two forms - as a string representing one of the native kyber validation rules (see below), or as an ValidateOpts object (see below) defining a rule.

```
// All validation rules take in some value and an options object
// Rules return true if they pass validation
type ValidationRuleType = (value: any, ValidateOpts) => boolean;

type FormatterType = (value?: FieldValue, opts?: Object) => string;
 
type ValidateOpts = {
    formatter?: FormatterType, // used to format a validation message when validation fails
    rule?: ValidationRuleType, // custom rule to be used
    type: string, // represents a kyber internal validation type,
    validationMessages?: { [string]: FormatterType }, // provide custom validation messages to override the built in versions,
		validationRules?: { [string]: ValidationRuleType }, // provide custom validation rules to override the built in versions
};
 
type GetComponentValidationType = () => Array<string | ValidateOpts>;
```

```

// use existing validation rules
const validation = ['minLength', 'isPhone'];
```

```
 
// functionally equivalent to
const validation = [{ type: 'minLength' }, { type: 'isPhone' }];
```
 
```
// custom rule
const justSayNo = (value, opts) => value !== 'Al' && opts.lastName !== 'Wolfson';
const justSayNoFormatter = (value, opts) => `${value} {opts.lastName} is not allowed`;
const validation = [{ rule: justSayNo, formatter: justSayNoFormatter  }];
 
// Results in 'Al Wolfson is not allowed' if the validation rule is called like validate('Al', { lastName: 'Wolfson' })
```

## Available Validation Rules
The following rules are available:

- hasValue
- minLength - validates against `minLen` attribute
- maxLength - validates against `maxLen` attribute
- minValue - validates against `minVal` attribute
- maxValue - validates against `maxVal` attribute
- range - validates value is between `min` and `max` attributes
- isInteger
- isNumeric
- isJSON
- isPhone
- required

## Custom validation

Both rules and formatters receive 2 args - the current `value` as well as an `opts` object. When used with Kyber `opts` contains all props for the FIELD (not the SmartField) as well as the validation rules and label from the SmartField.

Validation rules should also receive a `formValue` item as part of the opts object - formValue should represent the entire form model, allowing cross field validation.

Example:
```
const myValidationRule = (value, opts) => !value || value === 123;
const myValidationFormatter = (value, { label }) => `Oh snap, ${label} can't be set to 123`;
```

Cross Field Example:
```
const myValidationRule = (value, opts) => !value || value < opts.formValue.someOtherField;
const myValidationFormatter = (value, { label }) => `Oh snap, ${label} can't be less than ${opts.formValue.someOtherField}`;
```

## Default decorators
### Depends on field value
Allows to run validation only when specified by `dependsOnField` form field has a value specified by `dependsOnValue`

An example of `required` rule
```javascript
// const formValue = {preRetirementGrossGrowth: {id: 'Custom'}}
// required rule will run only if `formValue.preRetirementGrossGrowth.id` has value 'Custom'
const validation = [{ type: 'required', dependsOnField: 'preRetirementGrossGrowth.id', dependsOnValue: 'Custom' }];
```
