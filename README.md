# Detecting Stock Market Crashes with Topological Data Analysis (TDA)

As long as there will be financial markets, there will be financial crashes. Most suffer when the market takes a dip… those who can foresee it can protect their assets or take risky short positions to make a profit (a nevertheless stressful situation to be in, as depicted in [The Big Short](https://www.youtube.com/watch?v=vgqG3ITMv1Q)).

An asset on the market is associated to a dynamical system, whose price varies in function of the available information. The price of an asset on a financial market is determined by a wide range of information, and in the [efficient market hypothesis](https://en.wikipedia.org/wiki/Efficient-market_hypothesis), a simple change in the information will be immediately priced in.

> *The dynamics of financial systems are comparable to those of physical systems*

In the same way that phase transitions occur between solids, liquids, and gases, we can discern a normal regime on the market from a chaotic one.

Observations show that financial crashes are preceded by a period of increased [oscillation in asset prices](https://arxiv.org/abs/cond-mat/9901035). This phenomenon translates into an abnormal change in the geometric structure of the time series.

In this post we use topological data analysis (TDA) to capture these geometric changes in the time series in order to produce a reliable detector for stock market crashes. The code is implemented mainly by [`Giotto-TDA`](https://github.com/giotto-ai/giotto-tda), an open-source library for topological data analysis.

### There is little consensus about the exact definition of a financial crash

Intuitively, stock market crashes are rapid drop in asset prices. The price drop is caused by massive selling of assets, which is an attempt to close positions before prices decrease even further.

The awareness of a large speculative bubble (like in the case of the sub-primes), or a catastrophic event, will cause markets to crash. In the last two decades we have seen two major crashes: the 2000 dot-com crash and the 2008 global financial crisis.

## The results

We analyze daily prices of the S&P 500 index from 1980 to the present day. The S&P is an index that is commonly used to benchmark the state of the financial market as it measures the stock performance of the 500 large-cap US companies.

This highlights one of the key motivations behind TDA, namely that topology and geometry can provide a powerful method to abstract subtle structure in complex data.

![fig1](/images/fig1.png)
[Detection of stock market crashes from baseline (left) and topological (right) models]

Let's describe in more detail the two approaches.

### A simple baseline

Given that market crashes represent a sudden decline of stock prices, one simple approach to detect these changes involves tracking the first derivative of average price values over a rolling window. Indeed, in the figure below we can see that this naïve approach already captures the Black Monday crash (1987), the burst of the dot-com bubble (2000–2004), and the financial crisis (2007–2008).

![fig2](/images/fig2.png)
[Magnitude of the first derivative of mean close prices between successive windows]

By normalizing this time series to take values in the [0,1] interval, we can apply a threshold to label points on our original time series where a crash occurred.

![fig3](/images/fig3.png)
[Crash probability for baseline model (left), with points above threshold shown on original time series (right)]

> *Evidently this simple method is rather noisy*

With many points labeled as a crash, following this advice will result in over-panicking and selling your assets too soon. Let's see if TDA can help us reduce the noise in the signal and obtain a more robust detector!

## The TDA Pipeline

The mathematics underlying TDA is deep and won't be covered in this project; but for our purposes, it is sufficient to think of TDA as a means to extract informative features which can be used for modeling downstream.

The pipeline consists in: 2) embedding the time series into a point cloud and constructing sliding windows of point clouds, 3) building a filtration on each window to have an evolving structure encoding the geometrical shape of each window, 4) extracting the relevant features of those windows using persistence homology, 5) comparing each window by measuring the difference of those features from one window to the next, 6) constructing an indicator of crash based on this difference.

