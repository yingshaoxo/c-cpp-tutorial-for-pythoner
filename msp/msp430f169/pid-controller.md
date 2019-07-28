# PID Controller

## Introduction

PID stands for **proportional–integral–derivative.**

We use this tech to insure an env variable to keep staying at our target value. For example, PID Controllers can be used in industry to regulate [temperature](https://en.wikipedia.org/wiki/Temperature), [pressure](https://en.wikipedia.org/wiki/Pressure), [force](https://en.wikipedia.org/wiki/Force), [feed rate](https://en.wikipedia.org/wiki/Feed_rate), [flow rate](https://en.wikipedia.org/wiki/Volumetric_flow_rate), chemical composition \(component [concentrations](https://en.wikipedia.org/wiki/Concentration)\), [weight](https://en.wikipedia.org/wiki/Weight), [position](https://en.wikipedia.org/wiki/Position_%28vector%29), [speed](https://en.wikipedia.org/wiki/Speed), and so on.

## Pseudocode

```c
previous_error = 0
integral = 0
loop:
  error = setpoint - measured_value
  integral = integral + error * dt
  derivative = (error - previous_error) / dt
  output = Kp * error + Ki * integral + Kd * derivative
  previous_error = error
  wait(dt)
  goto loop
```

This could be extremely difficult to implement in single-chip micro-controller. So maybe I'll cover it in the end of this series.

## References:

{% embed url="https://en.wikipedia.org/wiki/PID\_controller" %}

{% embed url="https://onion.io/2bt-pid-control-python/" %}

{% embed url="https://github.com/ivmech/ivPID" %}

{% embed url="https://github.com/m-lundberg/simple-pid" %}



