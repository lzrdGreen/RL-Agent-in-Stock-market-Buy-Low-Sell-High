# RL-Agent-in-Stock-market-Buy-Low-Sell-High

Reinforcement Learning for playing in the stock market is often considered as a continuing task. But is it always true? From the point of 
view of an individual investor all investments are episodic: one day you need your money back. I am going to investigate a model using 
real market data. I mean SP500 data. To do this, I download SP500 ETF data from iShares (from May 15, 2000 to April 19, 2018): 
https://www.ishares.com/us/products/239726/ishares-core-sp-500-etf

They give all history of the values of the fund at the end of the trading day which is not exactly the closing price but very close 
indeed (otherwise an arbitarge would become possible which could be immediately exloited). During the trading day the price fluctuates, 
but in principle this doesn't matter (just more points in the history).

The best known strategy is "buy low - sell high". But what exactly is low/ high?

There is no other way to answer this question  than to look at historic data. I take medians for the previous year (to be precise for the
previous 253 trading days which is around one year), 1st and 3rd quantiles. The current price is compared to median minus or plus the 
distance between the quantile and the median with some factor.

Share purchase is a discrete action, you can purchase or sell only an integer number of shares. However, these numbers are pretty large.
As a first approximation the Agent is allowed to buy the shares for all his money or sell all shares at once (or keep them, of course).
As I have no good intuition for the task, I'd prefer somethig very simple before moving to Deep-Q or Agent-Critic algorithms.

So in notebooks MC 1 and MC 2 I run Monte Carlo simulations where the price range is split in several "baskets", the further the basket 
from the median the higher the corresponding probability of an action (buy if price is low and you have free money, sell if the price is 
high and you have shares). To avoid too much trade, the Agent pays a fee for each transaction (9.99 USD). Interstingly, these stochastic 
policies seem to degenerate into deterministic one!

Buy if the current price is less than the 1st quantile, sell if the price is higher than the 3rd quantile.

The notebook Deter eplores the deterministic policy - if the thresholds for actions exactly coincide with the quantiles (not exactly but 
very close)

At first it may seem strange that a very noisy ETF share price history results in a deterministic policy. However, we didn't learn 
anything from running the episodes so I just get some hint about optimal values to sell or buy. A more sopisticated (and greedy) investor 
may know about bull runs (and terrifying bear runs). If the price goes up, it may well happen that it'll continue to rise. Maybe it's 
reasonable to sell just half of the available shares and wait for even higher price to sell the rest? Similarly, if the price drops 
invest half of the money and wait for an even better price. If the price goes up, you miss an opportunity to invest and the profit from 
sale of just half the shares is twice less. So the game is about the balance sell all or sell half and buy for 100% or just 50% of your 
money. But it's a task for a future notebook.


