# Double Exponential Numerical Integration Library

This library provides a numerical integration method by Double Exponential (DE) formula.

On DE formula, a integration is approximated as Eq. (1),

<img src="https://latex.codecogs.com/gif.latex?(1)&space;\int_{a}^{b}&space;f(x)&space;dx&space;\approx&space;\int_{t_a}^{t_b}&space;f(\phi(t))\phi'(t)&space;dt&space;\approx&space;\sum_{k=0}^{N-1}&space;f(x_k)w_k" title="(1) \int_{a}^{b} f(x) w(x) dx \approx \int_{t_a}^{t_b} f(\phi(t))\phi'(t) dt \approx \sum_{k=0}^{N-1} f(x_k)w_k" />

where

<img src="https://latex.codecogs.com/gif.latex?t_k&space;=&space;\frac{t_b&space;-&space;t_a}{N-1}k&space;&plus;&space;t_a" title="t_k = \frac{t_b - t_a}{N-1}k + t_a" />, 
<img src="https://latex.codecogs.com/gif.latex?x_k&space;=&space;\phi(t_k)" title="x_k = \phi(t_k)" />, 
<img src="https://latex.codecogs.com/gif.latex?w_k&space;=&space;\frac{t_b-t_a}{N-1}&space;\phi'(t_k)" title="w_k = \frac{t_b-t_a}{N-1} \phi'(t_k)" />.

In this library, Eq.(1) can computed by the following code:

```d
import deint;

// Now we assume that a, b, w, N, t_a, t_b, and f are defined as Eq.(1).
auto deint = makeDEInt!real(a, b, No.isExpDecay, N, t_a, t_b);

// compute Eq.(1)
real ans = deint.integrate((real x) => f(x));
```

* The default values of `N`, `t_a`, and `t_b` are `100`, `-5`, and `5`, respectively.
* If the integrand function `f(x)` is an exponential-decay function on `|x| -> infinity`, you should change the flag `No.isExpDecay` to `Yes.isExpDecay`.


For example:

+ Integrate f(x) on (0, 1).

<img src="https://latex.codecogs.com/gif.latex?(2)&space;\int_0^1&space;f(x)&space;dx&space;\approx&space;\int_{-5}^{5}&space;f(\phi(t))&space;\phi'(t)&space;dt&space;\approx&space;\sum_{k=0}^{99}&space;f(x_k)&space;w_k" title="(2) \int_0^1 f(x) dx \approx \int_{-5}^{5} f(\phi(t)) \phi'(t) dt \approx \sum_{k=0}^{99} f(x_k) w_k" />

where

<img src="https://latex.codecogs.com/gif.latex?\phi(t)=\frac{1}{2}&space;\tanh(\frac{\pi}{2}&space;\sinh(t))&plus;\frac{1}{2}" title="\phi(t)=\frac{1}{2} \tanh(\frac{\pi}{2} \sinh(t))+\frac{1}{2}" />

<img src="https://latex.codecogs.com/gif.latex?\phi'(t)=\frac{\pi}{4}&space;\frac{\cosh(t)}{\cosh^2(\frac{\pi}{2}\sinh(t))}" title="\phi'(t)=\frac{\pi}{4} \frac{\cosh(t)}{\cosh^2(\frac{\pi}{2}\sinh(t))}" />

and 

<img src="https://latex.codecogs.com/gif.latex?t_k&space;=&space;\frac{10}{99}k-5" title="t_k = \frac{10}{99}k-5" />, 
<img src="https://latex.codecogs.com/gif.latex?x_k&space;=&space;\phi(t_k)" title="x_k = \phi(t_k)" />, 
<img src="https://latex.codecogs.com/gif.latex?w_k&space;=&space;\frac{10}{99}&space;\phi'(t_k)" title="w_k = \frac{10}{99} \phi'(t_k)" />.


```d
// DEInt!real is a struct which computes x_k and w_k in advance.
auto int01 = makeDEInt!real(0, 1);

// When f(x) = x, int_0^1 x dx = 0.5
assert(int01.integrate((real x) => x).approxEqual(0.5));

// `DEInt!real` is reusable.
// When f(x) = x^^2, int_0^1 x^^2 dx = 1/3
assert(int01.integrate((real x) => x^^2).approxEqual(1/3.0));
```


+ Integrate f(x) on (-inf, inf)

<img src="https://latex.codecogs.com/gif.latex?\int_{-\infty}^{\infty}&space;f(x)&space;dx&space;\approx&space;\int_{-5}^{5}&space;f(\phi(t))&space;\phi'(t)&space;dt&space;\approx&space;\sum_{k=0}^{99}&space;f(x_k)&space;w_k" title="\int_{-\infty}^{\infty} f(x) dx \approx \int_{-5}^{5} f(\phi(t)) \phi'(t) dt \approx \sum_{k=0}^{99} f(x_k) w_k" />

where

<img src="https://latex.codecogs.com/gif.latex?\phi(t)=\sinh(\frac{\pi}{2}\sinh(t))" title="\phi(t)=\sinh(\frac{\pi}{2}\sinh(t))" />

<img src="https://latex.codecogs.com/gif.latex?\phi'(t)=\frac{\pi}{2}\cosh(t)&space;\cosh(\frac{\pi}{2}\sinh(t))" title="\phi'(t)=\frac{\pi}{2}\cosh(t) \cosh(\frac{\pi}{2}\sinh(t))" />

and 

<img src="https://latex.codecogs.com/gif.latex?t_k&space;=&space;\frac{10}{99}k-5" title="t_k = \frac{10}{99}k-5" />, 
<img src="https://latex.codecogs.com/gif.latex?x_k&space;=&space;\phi(t_k)" title="x_k = \phi(t_k)" />, 
<img src="https://latex.codecogs.com/gif.latex?w_k&space;=&space;\frac{10}{99}&space;\phi'(t_k)" title="w_k = \frac{10}{99} \phi'(t_k)" />.


```d
// integration on [-inf, inf]
auto intII = makeDEInt!real(-real.infinity, real.infinity);

// Gaussian integral
assert(intII.integrate((real x) => exp(-x^^2)).approxEqual(sqrt(PI)));
```

+ Integrate f(x) = g(x) exp(-x) on (1, inf)

<img src="https://latex.codecogs.com/gif.latex?\int_{1}^{\infty}&space;f(x)&space;dx&space;\approx&space;\int_{-5}^{5}&space;f(\phi(t))&space;\phi'(t)&space;dt&space;\approx&space;\sum_{k=0}^{99}&space;f(x_k)&space;w_k" title="\int_{1}^{\infty} f(x) \exp(-x) dx \approx \int_{-5}^{5} f(\phi(t)) \exp(-\phi(t)) \phi'(t) dt \approx \sum_{k=0}^{99} f(x_k) w_k" />

where

<img src="https://latex.codecogs.com/gif.latex?\phi(t)&space;=&space;\exp(t-\exp(-t))&plus;1" title="\phi(t) = \exp(t-\exp(-t))+1" />

<img src="https://latex.codecogs.com/gif.latex?\phi'(t)&space;=&space;(1&plus;\exp(-t))&space;\exp(t-\exp(-t))" title="\phi'(t) = (1+\exp(-t)) \exp(t-\exp(-t))" />

and 

<img src="https://latex.codecogs.com/gif.latex?t_k&space;=&space;\frac{10}{99}k-5" title="t_k = \frac{10}{99}k-5" />, 
<img src="https://latex.codecogs.com/gif.latex?x_k&space;=&space;\phi(t_k)" title="x_k = \phi(t_k)" />, 
<img src="https://latex.codecogs.com/gif.latex?w_k&space;=&space;\frac{10}{99}&space;\phi'(t_k)" title="w_k = \frac{10}{99} \phi'(t_k)" />.

```d
// integrate f(x) = g(x)exp(-x) on (1, inf)
// Now, we know that the integrand f(x) decay exponentially.
auto intFI = makeDEInt!real(1, real.infinity, Yes.isExpDecay);

// incomplete gamma function
assert(intFI.integrate((real x) => x * exp(-x)).approxEqual(gammaIncompleteCompl(2, 1) * gamma(2)));

// Also, we can use `withWeight` which pre-computes and stores weight function.
// The `withWeight` is useful when integrands have same weights.
auto intFIW = intFI.withWeight((real x) => exp(-x));

// incomplete gamma function
assert(intFIW.integrate((real x) => x).approxEqual(gammaIncompleteCompl(2, 1) * gamma(2)));
assert(intFIW.integrate((real x) => x^^2).approxEqual(gammaIncompleteCompl(3, 1) * gamma(3)));
assert(intFIW.integrate((real x) => x^^3).approxEqual(gammaIncompleteCompl(4, 1) * gamma(4)));
```
