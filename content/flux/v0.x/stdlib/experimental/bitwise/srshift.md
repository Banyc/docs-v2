---
title: bitwise.srshift() function
description: >
  `bitwise.srshift()` shifts the bits in `a` right by `b` bits.
  Both `a` and `b` are integers.
menu:
  flux_0_x_ref:
    name: bitwise.srshift
    parent: experimental/bitwise
    identifier: experimental/bitwise/srshift
weight: 201
---

<!------------------------------------------------------------------------------

IMPORTANT: This page was generated from comments in the Flux source code. Any
edits made directly to this page will be overwritten the next time the
documentation is generated. 

To make updates to this documentation, update the function comments above the
function definition in the Flux source code:

https://github.com/influxdata/flux/blob/master/stdlib/experimental/bitwise/bitwise.flux#L427-L427

Contributing to Flux: https://github.com/influxdata/flux#contributing
Fluxdoc syntax: https://github.com/influxdata/flux/blob/master/docs/fluxdoc.md

------------------------------------------------------------------------------->

`bitwise.srshift()` shifts the bits in `a` right by `b` bits.
Both `a` and `b` are integers.

{{% warn %}}
#### Deprecated
`srshift` is deprecated in favor of [`bitwise`](/flux/v0.x/stdlib/bitwise/srshift/).
{{% /warn %}}

##### Function type signature

```js
(a: int, b: int) => int
```

{{% caption %}}For more information, see [Function type signatures](/flux/v0.x/function-type-signatures/).{{% /caption %}}

## Parameters

### a
({{< req >}})
Left hand operand.



### b
({{< req >}})
Number of bits to shift.




## Examples

- [Shift bits right in an integer](#shift-bits-right-in-an-integer)
- [Shift bits right in integers in a stream of tables](#shift-bits-right-in-integers-in-a-stream-of-tables)

### Shift bits right in an integer

```js
import "experimental/bitwise"

bitwise.srshift(a: 1234, b: 2)// Returns 308


```


### Shift bits right in integers in a stream of tables

```js
import "experimental/bitwise"
import "sampledata"

sampledata.int()
    |> map(fn: (r) => ({r with _value: bitwise.srshift(a: r._value, b: 3)}))

```

{{< expand-wrapper >}}
{{% expand "View example input and ouput" %}}

#### Input data

| _time                | _value  | *tag |
| -------------------- | ------- | ---- |
| 2021-01-01T00:00:00Z | -2      | t1   |
| 2021-01-01T00:00:10Z | 10      | t1   |
| 2021-01-01T00:00:20Z | 7       | t1   |
| 2021-01-01T00:00:30Z | 17      | t1   |
| 2021-01-01T00:00:40Z | 15      | t1   |
| 2021-01-01T00:00:50Z | 4       | t1   |

| _time                | _value  | *tag |
| -------------------- | ------- | ---- |
| 2021-01-01T00:00:00Z | 19      | t2   |
| 2021-01-01T00:00:10Z | 4       | t2   |
| 2021-01-01T00:00:20Z | -3      | t2   |
| 2021-01-01T00:00:30Z | 19      | t2   |
| 2021-01-01T00:00:40Z | 13      | t2   |
| 2021-01-01T00:00:50Z | 1       | t2   |


#### Output data

| _time                | _value  | *tag |
| -------------------- | ------- | ---- |
| 2021-01-01T00:00:00Z | -1      | t1   |
| 2021-01-01T00:00:10Z | 1       | t1   |
| 2021-01-01T00:00:20Z | 0       | t1   |
| 2021-01-01T00:00:30Z | 2       | t1   |
| 2021-01-01T00:00:40Z | 1       | t1   |
| 2021-01-01T00:00:50Z | 0       | t1   |

| _time                | _value  | *tag |
| -------------------- | ------- | ---- |
| 2021-01-01T00:00:00Z | 2       | t2   |
| 2021-01-01T00:00:10Z | 0       | t2   |
| 2021-01-01T00:00:20Z | -1      | t2   |
| 2021-01-01T00:00:30Z | 2       | t2   |
| 2021-01-01T00:00:40Z | 1       | t2   |
| 2021-01-01T00:00:50Z | 0       | t2   |

{{% /expand %}}
{{< /expand-wrapper >}}
