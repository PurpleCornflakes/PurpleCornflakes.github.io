---
layout: post
title: Solve ODE in python
category: python
---

{% highlight Python linos %}
from scipy.integrate import odeint
import numpy as np
import matplotlib.pyplot as plt
# an array of t to be feed into model one by one
tt = np.linspace(0,10,100)
# y is array of x[t], y[t]
# in 2nd order case, x could be dy/dt
def model(y, t, a, b, c, d):
    return [y[0]*(a-b*y[1]), -y[1]*(c-d*y[0])]
y0 = [3,1]
# args to be feed into model
a,b,c,d = 1,1,1,1
# contains xs, ys
sol = odeint(model, y0, tt, args = (a,b,c,d))
plt.plot(tt, sol[:,0])
plt.plot(tt, sol[:,1])
plt.show()
{% endhighlight %}