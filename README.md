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

The notebook Deter explores the deterministic policy - if the thresholds for actions exactly coincide with the quantiles (not exactly but very close)

At first it may seem strange that a very noisy ETF share price history results in a deterministic policy. However, we didn't learn 
anything from running the episodes so I just get some hint about optimal values to sell or buy. A more sopisticated (and greedy) investor may know about bull runs (and terrifying bear runs). If the price goes up, it may well happen that it'll continue to rise. Maybe it's reasonable to sell just half of the available shares and wait for even higher price to sell the rest? Similarly, if the price drops invest half of the money and wait for an even better price. If the price goes up, you miss an opportunity to invest and the profit from sale of just half the shares is twice less. So the game is about the balance sell all or sell half and buy for 100% or just 50% of your money. This strategy was implemented in MC 3 notebook. In effect, it discretizes the action space (the Agent can buy/ sell an integer number of shares) which is large into a very simple three state system - all cash, almost all shares of ETF and approximately "half cash, half shares". This model produced better results than MC1 and MC2.

I also investigated an even more sophisticated model in MC 3. The Agent could remember how it got into state S2. Thus it could determine if a bull or bear run is under way. This allowed to choose the thresholds for sale/ urchase of shares from state S2 more aggressively. Of course this resulted in significantly better return. What was unexpected, the Agent ended in a determitistic policy, its best strategy was: start at S1 (all cash) then buy shares for half the money. When the Agent gets to state S3 (almost all shares, i.e. fully invested) it moves to S2 (half cash, half shares). How the Agent behaves in state S2 is determined by the external parameter - current price of the ETF.

The behaviour of the Agent in the second model in MC 3 is influenced by the previous action of the Agent which means that the system has lost its Markov property (memorilessness). It's a problem. To tackle it I could think about states S1, S2 and S3 as of pairs of aliased states which correspond to different market sentiments. But before testing this hypothesis, I'll need to figure out how to determine the 
value of the market sentiment parameter (positive, neutral, negative) from the price movement - before the current date.


